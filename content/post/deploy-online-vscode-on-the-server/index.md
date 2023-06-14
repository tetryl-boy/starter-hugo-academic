---
title: Deploy online VSCode on the server
date: 2022-07-14T07:15:46.520Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
## Background
Carrying a disk around is not na elegant way for coding. The best place to code is not on you personal devices, it should be on the cloud, not only for better preservation but also for the unified computer environment. With a online IDE, you can even code on you Ipad. Such a cool thing!


## Install code-server

To have you own onling IDE, you should have a server with full root access(for deploying firewall and nignx). It should not be too expensive to rent a VPS with 2 cores and 30 GB disk.

Go to the repository **[code-server](https://github.com/coder/code-server/releases/tag/v4.10.1)**, then pick the right version. Download to your server.

```bash
wget https://github.com/coder/code-server/releases/download/v4.10.1/code-server-4.10.1-linux-amd64.tar.gz
```
 Then 
 ```bash
#extract the files
 tar -xzvf code-server-4.10.1-linux-amd64.tar.gz
#rename the folder
 mv code-server-4.10.1-linux-amd64 code-server 
 ```

 run the code server
 ```bash
cd code-server
./code-server
 ```
Then use `Ctrl+C` to stop

```bash 
vim  ~/.config/code-server/config.yaml
```

```yaml
bind-addr: 0.0.0.0:9999     #choose the port you want, remenber to open it in firewall
auth: password
password:                   #your password
cert: fullchain.pem   # full path of your ssl cert and cert key
cert-key: privkey.pem
```

## Install pm2

install pm2 
```bash
npm install pm2 -g
```

Write start script
```bash 
cd code-server
echo "./bin/code-server" > start_code_server.sh
pm2 start start_code_server.sh
```
use `pm2 list` to monitor the process

You should be able to access you codeserver at (http://yourIP:port) now.

## Deploy Nignx

Create `code-server.conf` to store the configuration for exposing code-server at your domain:

```bash
server {
	listen 80;
	listen [::]:80;

	server_name code-server.your_domain;

	location / {
		proxy_pass http://localhost:8080/;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection upgrade;
		proxy_set_header Accept-Encoding gzip;
	}
}
```

Replace code-server.your_domain with your desired domain, then save and close the file.

To make this site configuration active, create a symlink of it:

```bash
sudo ln -s /etc/nginx/sites-available/code-server.conf /etc/nginx/sites-enabled/code-server.conf
```
Test the validity of the configuration:
```bash
sudo nginx -t
```
You’ll see the following output:

Output
```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
For the configuration to take effect, restart Nginx:
```bash
sudo systemctl restart nginx
```


## TLS certificatre
Now you’ll secure your domain using a Let’s Encrypt TLS certificate.

Add the Certbot package repository to your server:
```bash
sudo add-apt-repository ppa:certbot/certbot
```
Install Certbot and its Nginx plugin:
```bash
sudo apt install python-certbot-nginx
```
Configure ufw to accept encrypted traffic:
```bash
sudo ufw allow https
```

Reload it for the configuration to take effect:
```bash
sudo ufw reload
```


To secure it, install a Let’s Encrypt TLS certificate using Certbot.

Request a certificate for your domain with:
```bash
sudo certbot --nginx -d code-server.your_domain
```
Provide an email address for urgent notices, accept the EFF’s Terms of Service, and decide whether to redirect all HTTP traffic to HTTPS.

