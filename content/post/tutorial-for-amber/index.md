---
title: Tutorial for Amber
subtitle: My Own Procedure for MD simulation
date: 2023-06-09T21:32:57.979Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
## Ligand parameterization

Ligand parameterization refers to the process of assigning force field parameters to a small molecule (ligand) in order to conduct molecular dynamics simulations or other computational studies. This process involves assigning partial charges, bond lengths, bond angles, dihedral angles, and other parameters to the atoms in the ligand so that they can be properly modeled using a force field. The process typically involves several steps, including converting the ligand structure to a suitable format, assigning atom types and charges, generating the ligand topology file, and validating the accuracy of the parameters through comparison with experimental data or previously reported computational results. 

C﻿reate a Gaussian Input file using the **docked** ligand structure.

G﻿aussian Input File:

```shell
%nproc=12
%mem=16GB
#p hf/6-31g(d) nosymm pop=mk iop(6/50=1,6/33=2,6/41=10,6/42=6) scf=tight

LIG

0 1
<xyz>

LIG.esp
```

A﻿fter the calculation, create a bash script like what is shown below and excute it. You will get two files, namely `.pdb`, `.frcmod`.

```shell
#$ -cwd
#$ -m bea
#$ -M EMAIL@ADDRESS.com
#$ -o prep.joblog
#$ -e prep.error

LIGAND=LIG
LIGAND_CHARGE=0

\##########################################################################################

export AMBERHOME=/usr/local/amber16/amber16

$AMBERHOME/bin/antechamber -i ${LIGAND}.out -fi gout -o ${LIGAND}.prepin -fo prepi -c resp -at gaff -rn ${LIGAND} -nc ${LIGAND_CHARGE}
$AMBERHOME/bin/antechamber -i ${LIGAND}.out -fi gout -o ${LIGAND}.pdb -fo pdb -at gaff -rn ${LIGAND}
$AMBERHOME/bin/parmchk -i ${LIGAND}.prepin -f prepi -o ${LIGAND}.frcmod

rm ANTECHAMBER* qout QOUT esout punch ATOMTYPE.INF PREP.INF NEWPDB.PDB
```

The `.frcmod` file is a file format used in Amber molecular dynamics simulations to modify or add force field parameters to the standard Amber force field. It stands for "Force Field Modification" and is used to add or modify parameters for specific atoms, bonds, angles, or dihedrals that are not included in the standard Amber force field.

O﻿pen the `LIG.pdb` and `protein.pdb` files in pymol and select `Action -> copy to object -> protein`. After that save the protein file as `protein_complex.pdb` by selecting `file -> Export Molecule` in the tool bar.

![](ligand.png "Copying Ligand to the Protein File")

## M﻿inimization

A﻿fter preparing the protein complex, we can use the scripts below to prepare the the files we need for simulation, it will add water and cations and will also create a box for the system. 

```shell
#$ -cwd
#$ -m bea
#$ -M EMAIL@ADDRESS.com
#$ -o setup.joblog
#$ -e setup.error


PROTEIN=5btu_complex                                    # Name of the protein. Must match name of PDB file
LIGAND=LIG                                              # Name of ligand. Must match 3 letter code in PDB file


#################################################################################################


export AMBERHOME=/usr/local/amber16/amber16

echo "Generating MD files for the protein: ${PROTEIN}"
echo "Parameters for the following ligands/nonstandard residues will be used:"
#echo "${LIGAND}"
echo "  "

##### TLEAP #####
echo "source leaprc.protein.ff14SB"                                                                >  tleap.in
echo "source leaprc.gaff"                                                                          >> tleap.in
echo "source leaprc.water.tip3p"                                                                   >> tleap.in
echo "loadAmberParams ${LIGAND}.frcmod"                                                         >> tleap.in
echo "loadAmberPrep     ${LIGAND}.prepin"                                                          >> tleap.in
echo "x = loadPDB ${PROTEIN}.pdb"                                                                  >> tleap.in
echo "savepdb x ${PROTEIN}_tleap.pdb"                                                           >> tleap.in
echo "center x"                                                                                    >> tleap.in
echo "alignaxes x"                                                                                 >> tleap.in
echo "addions x Cl- 0"                                                                             >> tleap.in
echo "addions x Na+ 0"                                                                             >> tleap.in
echo "solvateoct x TIP3PBOX 10"                                                                    >> tleap.in
echo "saveamberparm x ${PROTEIN}_wat.prmtop ${PROTEIN}_wat.inpcrd"      >> tleap.in
echo "savepdb x ${PROTEIN}_wat.pdb"                                                                >> tleap.in
echo "quit"                                                                                        >> tleap.in
$AMBERHOME/bin/tleap -f tleap.in -s
rm *nonprot.pdb *renum.txt *water.pdb *sslink* tleap.in leap.log #removes log files

mkdir run/
```

* `source leaprc.protein.ff14SB`: This command loads the force field parameters for the protein from the leaprc.protein.ff14SB file.
* `source leaprc.gaff`: This command loads the force field parameters for the ligand from the leaprc.gaff file.
* `source leaprc.water.tip3p`: This command loads the force field parameters for water molecules from the leaprc.water.tip3p file.
* `loadAmberParams ${LIGAND}.frcmod`: This command loads additional force field parameters for the ligand from the ${LIGAND}.frcmod file.
* `loadAmberPrep ${LIGAND}.prepin`: This command loads the topology file for the ligand from the ${LIGAND}.prepin file.
* `x = loadPDB ${PROTEIN}.pdb`: This command loads the protein structure from the ${PROTEIN}.pdb file into a variable called x.
* `savepdb x ${PROTEIN}_tleap.pdb`: This command saves the protein structure loaded in the previous step to a new PDB file called ${PROTEIN}_tleap.pdb.
* `center x`: This command centers the protein-ligand complex in the simulation box.
* `alignaxes x`: This command aligns the principal axes of the protein-ligand complex with the simulation box axes.
* `addions x Cl- 0`: This command adds chloride ions to the system to neutralize the net charge of the complex.
* `addions x Na+ 0`: This command adds sodium ions to the system to achieve the desired salt concentration.
* `solvateoct x TIP3PBOX 10`: This command solvates the protein-ligand complex in a cubic water box of size 10 Angstroms.
* `saveamberparm x ${PROTEIN}_wat.prmtop ${PROTEIN}_wat.inpcrd`: This command saves the topology and coordinate files for the solvated protein-ligand complex in Amber format.
* `savepdb x ${PROTEIN}_wat.pdb`: This command saves the solvated protein-ligand complex in PDB format.
* `quit`: This command exits the tleap program.

## M﻿inimization

```shell
initial minimization
 &cntrl
  imin   = 1, irest = 0, ntx = 1,
  maxcyc = 50000, ncyc = 25000,
  ntc    = 1, ntf = 1,
  cut    = 8.0,
  ntb    = 1,
  iwrap  = 1,
  ig     = -1,
  ioutfm = 1,
  ntpr   = 500, ntwx = 500, ntwr = 500,
  ntr    = 1, restraint_wt = 2.0,
  restraintmask = ':1-181@C,CA,N|:182&!@H=',
  nmropt = 1,
 /
 &wt
  type='END',
 &end

```



* `imin = 1`: This specifies that this is a minimization job, where the coordinates of the atoms are adjusted to reach a local minimum energy state.
* `irest = 0`: This specifies that this is a new simulation, not a restart from a previous simulation.
* `ntx = 1`: This specifies that the initial coordinates are being read from the input file.
* `maxcyc = 50000`: This specifies the maximum number of minimization cycles to perform.
* `ncyc = 25000`: This specifies the number of minimization cycles to perform.
* `ntc = 1`: This specifies that the SHAKE algorithm is being used to constrain bond lengths involving hydrogen atoms.
* `ntf = 1`: This specifies that the force field parameters are being calculated using the AMBER force field.
* `cut = 8.0`: This specifies the non-bonded cutoff distance in Angstroms.
* `ntb = 1`: This specifies that periodic boundary conditions are being used.
* `iwrap = 1`: This specifies that the coordinates of the atoms are being wrapped back into the simulation box after each step.
* `ig = -1`: This specifies the random number seed for the simulation.
* `ioutfm = 1`: This specifies the format for the output files.
* `ntpr = 500`: This specifies the frequency (in steps) of printing output to the log file.
* `ntwx = 500`: This specifies the frequency (in steps) of writing the coordinates to the trajectory file.
* `ntwr = 500`: This specifies the frequency (in steps) of writing the restart file.
* `ntr = 1`: This specifies that positional restraints are being applied.
* `restraint_wt = 2.0`: This specifies the weight of the restraints.
* `restraintmask = ':1-181@C,CA,N|:182&!@H='`: This specifies the atoms that are being restrained.
* `nmropt = 1`: This specifies that the SHAKE algorithm is being used to constrain bond lengths involving hydrogen atoms.
* `type='END'`: This specifies the end of the input file.



## H﻿eating

```shell
1ns heating in NVT
 &cntrl
  imin   = 0, irest = 0, ntx = 1,
  ntc    = 2, ntf = 2,
  cut    = 8.0,
  ntt    = 3, gamma_ln = 5.0,
  tempi  = 0.0, temp0  = 300.0,
  ntb    = 1,
  iwrap  = 1,
  ig     = -1,
  ioutfm = 1,
  nstlim = 1000000, dt = 0.001,
  ntpr   = 50000, ntwx = 50000, ntwr = 50000,
  ntr    = 1, restraint_wt = 2.0,
  restraintmask = ':1-181@C,CA,N|:182&!@H=',
  nmropt = 1,
 /
 &wt TYPE='TEMP0', istep1=0, istep2=1000000,
 value1=0.0, value2=300.0, /
 &wt TYPE='END',
 &end
DISANG=DIS.rst
&wt TYPE='END' /

```



* `gamma_ln = 5.0`: This specifies the collision frequency for the Langevin thermostat.
* `tempi = 0.0`: This specifies the initial temperature for the simulation.
* `temp0 = 300.0`: This specifies the desired temperature for the simulation.

## P﻿roduction

```shell
100ns production in NPT
 &cntrl
  imin   = 0, irest = 1, ntx = 5,
  ntc    = 2, ntf = 2,
  cut    = 8.0,
  ntt    = 3, gamma_ln = 5.0,
  tempi  = 300.0, temp0 = 300.0,
  ntb    = 2, ntp = 1, barostat = 2, taup=5.0,
  pres0  = 1.0,
  iwrap  = 1,
  nmropt = 1,
  ig     = -1,
  ioutfm = 1,
  nstlim = 50000000, dt = 0.002,
  ntpr   = 25000, ntwx = 25000, ntwr = 25000,
 /
 &wt
  type='END',
 &end

```



## S﻿tart Simulation

E﻿xcute the script below to start the simulation.

```shell
#$ -cwd
#$ -m bea
#$ -M example@email.com
#$ -q  gtx2080ti_gpu.q
#$ -o MD.joblog
#$ -e MD.error

export AMBERHOME=/usr/local/amber16/amber16
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64
                                                                                                 
#/usr/local/amber16/amber16//bin/pmemd.cuda -O -i min.in  -o min.out   -p *.prmtop -c *.inpcrd -r min.rst   -x min.nc   -ref *.inpcrd                             
#/usr/local/amber16/amber16//bin/pmemd.cuda -O -i eq1.in  -o eq1.out   -p *.prmtop -c min.rst  -r eq1.rst   -x eq1.nc   -ref min.rst              
#/usr/local/amber16/amber16//bin/pmemd.cuda -O -i eq2.in  -o eq2.out   -p *.prmtop -c eq1.rst  -r eq2.rst   -x eq2.nc   -ref eq1.rst                                                                                                   
#/usr/local/amber16/amber16//bin/pmemd.cuda -O -i eq3.in  -o eq3.out   -p *.prmtop -c eq2.rst  -r eq3.rst   -x eq3.nc   -ref eq2.rst
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod1.out -p *.prmtop -c eq3.rst  -r prod1.rst -x prod1.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod2.out -p *.prmtop -c prod1.rst -r prod2.rst -x prod2.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod3.out -p *.prmtop -c prod2.rst -r prod3.rst -x prod3.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod4.out -p *.prmtop -c prod3.rst -r prod4.rst -x prod4.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod5.out -p *.prmtop -c prod4.rst -r prod5.rst -x prod5.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod6.out -p *.prmtop -c prod5.rst -r prod6.rst -x prod6.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod7.out -p *.prmtop -c prod6.rst -r prod7.rst -x prod7.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod8.out -p *.prmtop -c prod7.rst -r prod8.rst -x prod8.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod9.out -p *.prmtop -c prod8.rst -r prod9.rst -x prod9.nc
/usr/local/amber16/amber16//bin/pmemd.cuda -O -i prod.in -o prod10.out -p *.prmtop -c prod9.rst -r prod10.rst -x prod10.nc
```