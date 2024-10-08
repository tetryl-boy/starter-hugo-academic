---
# Leave the homepage title empty to use the site title
title: Qinan Huang
date: 2022-10-24
type: landing

sections:

  - block: about.biography
    id: about
    content:
      title: Biography
      # Choose a user profile to display (a folder name within `content/authors/`)
      username: admin
  - block: features
    content:
      title: Skills
      items:
        - name: Python
          description: 90%
          icon: python
          icon_pack: fab
        - name: Molecule Dynamics
          description: 80%
          icon: dna
          icon_pack: fas
        - name: Machine Learning
          description: 60%
          icon: robot
          icon_pack: fas
        - name: Quantem Mechanics
          description: 100%
          icon: atom
          icon_pack: fas
  - block: experience
    content:
      title: Experience
      # Date format for experience
      #   Refer to https://wowchemy.com/docs/customization/#date-format
      date_format: Jan 2006
      # Experiences.
      #   Add/remove as many `experience` items below as you like.
      #   Required fields are `title`, `company`, and `date_start`.
      #   Leave `date_end` empty if it's your current employer.
      #   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.
      items:
        - title: Undergraduate Researcher
          company: Dilabio's Lab, The University of British Columbia
          company_url: ''
          company_logo: UBC
          location: British Columbia, Canada
          date_start: '2022-12-01'
          date_end: ''
          description:  |2-
            * Developed system specific ACPs(Atom-Centered Potentials) for quantum dynamics and vibrational spectroscopy;
            * Applied database to delta machine learning.
        - title: Undergraduate Researcher
          company: Hartwig's Lab, University of California, Berkeley
          company_url: ''
          company_logo: UCB
          location: California
          date_start: '2023-02-01'
          date_end: '2023-05-12'
          description:  |2-
            * Mechanism study of non-canonical Ullmann coupling.
            * DFT calculation for Copper catalyst with significant multi-reference characters.
        - title: Undergraduate Researcher
          company: Qu's Lab, Hunan University
          company_url: ''
          company_logo: 
          location: Hunan, China
          date_start: '2023-12-01'
          date_end: '2022-04-01'
          description:  |2-
            * Studied mechanism and control methods of metal-organic catalytic reactions.
            * Computational design of privileged chiral catalyst.

    design:
      columns: '2'
  - block: accomplishments
    content:
      # Note: `&shy;` is used to add a 'soft' hyphen in a long heading.
      title: 'Awards'
      subtitle:
      # Date format: https://wowchemy.com/docs/customization/#date-format
      date_format: Jan 2006
      # Accomplishments.
      #   Add/remove as many `item` blocks below as you like.
      #   `title`, `organization`, and `date_start` are the required parameters.
      #   Leave other parameters empty if not required.
      #   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.
      items:
        - certificate_url: 
          date_end: ''
          date_start: '2021-12-01'
          description: 'The highest level scholarship for Chinese university students'
          organization: Ministry of Education of the People’s Republic of China
          organization_url: 
          title: National Scholarship 
          url: ''
        - certificate_url: https://jamboree.igem.org/2022/results/medals
          date_end: ''
          date_start: '2022-10-01'
          description: 'International Genetically Engineered Machine (iGEM) competition'
          organization: IGEM
          organization_url: https://igem.org/
          title: IGEM 2022 Competition Silver Medal
          url: ''
        - certificate_url: https://www.comap-math.com/mcm/2022Certs/2212558.pdf
          date_end: ''
          date_start: '2022-02-01'
          description: 'Mathematical Contest In Modeling'
          organization: Consortium for Mathematics and Its Applications，COMAP
          organization_url: https://igem.org/
          title: MCM 2022 Meritorious Winner （7%）
          url: ''
    design:
      columns: '2'
  - block: collection
    id: posts
    content:
      title: Recent Posts
      subtitle: ''
      text: ''
      # Choose how many pages you would like to display (0 = all pages)
      count: 5
      # Filter on criteria
      filters:
        folders:
          - post
        author: ""
        category: ""
        tag: ""
        exclude_featured: false
        exclude_future: false
        exclude_past: false
        publication_type: ""
      # Choose how many pages you would like to offset by
      offset: 0
      # Page order: descending (desc) or ascending (asc) date.
      order: desc
    design:
      # Choose a layout view
      view: compact
      columns: '2'
  - block: portfolio
    id: projects
    content:
      title: Projects
      filters:
        folders:
          - project
      # Default filter index (e.g. 0 corresponds to the first `filter_button` instance below).
      default_button_index: 0
      # Filter toolbar (optional).
      # Add or remove as many filters (`filter_button` instances) as you like.
      # To show all items, set `tag` to "*".
      # To filter by a specific tag, set `tag` to an existing tag name.
      # To remove the toolbar, delete the entire `filter_button` block.
      buttons:
        - name: All
          tag: '*'
        - name: Deep Learning
          tag: Deep Learning
        - name: Other
          tag: Demo
    design:
      # Choose how many columns the section has. Valid values: '1' or '2'.
      columns: '1'
      view: showcase
      # For Showcase view, flip alternate rows?
      flip_alt_rows: false
  # - block: markdown
  #   content:
  #     title: Gallery
  #     subtitle: ''
  #     text: |-
  #       {{< gallery album="demo" >}}
  #   design:
  #     columns: '1'
  - block: collection
    id: featured
    content:
      title: Featured Publications
      filters:
        folders:
          - publication
        featured_only: true
    design:
      columns: '2'
      view: card
  - block: collection
    content:
      title: Recent Publications
      text: |-
        {{% callout note %}}
        Quickly discover relevant content by [filtering publications](./publication/).
        {{% /callout %}}
      filters:
        folders:
          - publication
        exclude_featured: true
    design:
      columns: '2'
      view: citation
  # - block: collection
  #   id: talks
  #   content:
  #     title: Recent & Upcoming Talks
  #     filters:
  #       folders:
  #         - event
  #   design:
  #     columns: '2'
  #     view: compact
  - block: tag_cloud
    content:
      title: Popular Topics
    design:
      columns: '2'
  - block: contact
    id: contact
    content:
      title: Contact
      subtitle:
      text: |-
        Contact me 
      # Contact (add or remove contact options as necessary)
      email: qinanhuang6@gmail.com
      phone: +1 (341) 766 9252
      appointment_url: 'https://calendly.com/qinanhuang'
      address:
        street: 619 Charles E Young Dr E
        city:  Los Angeles
        region: CA
        postcode: '90095'
        country: United States
        country_code: US
      directions: '5240'
      # office_hours:
      #   - 'Monday 10:00 to 13:00'
      #   - 'Wednesday 09:00 to 10:00'
      contact_links:
        - icon: twitter
          icon_pack: fab
          name: DM Me
          link: 'https://twitter.com/Twitter'
        - icon: skype
          icon_pack: fab
          name: Skype Me
          link: 'skype:echo123?call'
        - icon: video
          icon_pack: fas
          name: Zoom Me
          link: 'https://zoom.com'
      # Automatically link email and phone or display as text?
      autolink: true
      # Email form provider
      form:
        provider: netlify
        formspree:
          id:
        netlify:
          # Enable CAPTCHA challenge to reduce spam?
          captcha: false
    design:
      columns: '2'
---
