---
title: Simulation of solution evaporation
date: 2024-07-24
mathjax: true
categories: 
- Tutorials
- Orca
---
This post is for the MD simulation of *solution evaporation* by **Gromacs**.

<!-- more -->
# Obtain force field

You can use default force-fileds provided by Gromacs, or generating by Q-force.

## Q-force

The command to run `Q-force`:

```
qforce <structure_file> -o option_file
```

After Q-force generates all the files, two files are needed for MD simulation:
 Structure: `gas.gro`
 Force-field: `<name>.itp`

# Building simulation box

## Building solute box

The solute box should be built base on the concentration of solute.
Normally $200 mg/mL$ is set.

1. *Set* the size of box.
2. Calculate how much solute molecules should be in the box.
3. Adding one molecule in the box:

```
gmx editconf -f <solute>.gro -o ouput_file -box x y z -bt cubic
```

4. Adding other solute molecules:

```
gmx insert-molecules -f file_tobe_inserted -ci structure_tobe_inserted -o output_file -box x y z -nmol numbers_of_molecules
```

## Building solvent box

The solvent box should be built base on the density of solvent.

1. *Calculate* the size of box.
2. Generate solvent box:

```
gmx editconf -f solvent_file -o output_file -box a a a -bt cubic -c -rotate a b c
```

3. Check if the solvent molecules connected with each other:

```
gmx genconf -f solvent_box -o output_file -nbox nx ny nz
```

If connected, change `-rotate a b c`

## Building solution box

Using the following code to generate solution box:

```
gmx solvate -cp solute -cs solvent -o output
```

Some optional parameters:
 `-scale x`: For setting the VDW interaction. Default value is `0.57`.

## Generate topology file

An example:

```
;
#include "/home/ufslab223/shuran/ForceField/forcefield.itp"

#include "/home/ufslab223/shuran/2311_kwak_MD/CzCzPh/Forcefield/CzCzPh_qforce.itp"
#include "/home/ufslab223/shuran/2311_kwak_MD/CzCzPh/Forcefield/toluene_qforce.itp"

[ system ]
; Name
CzCzPh

[ molecules ]
; Compound       #mols
CzCzP 295
tolue 3273
```

# Pre-equilibrium

## Energy minimization

### Preprocess

#### Edit `.mdp` file

An example:

```
;
; GROMACS
; Energy Minimization Script
;
;
define  = -DFLEXIBLE ; pass to preprocessor
cpp  = usr/bin/cpp ; location of preprocessor
constraints = none
integrator = steep  ; steepest decents minimum (else cg)
nsteps  = 1000
;
; Energy Minimizing Stuff
;
emtol  = 10    ; convergence total force(kJ/mol/nm) is smaller than
emstep  = 0.01  ; initial step size (nm)
nstcomm  = 100  ; frequency or COM motion removal
ns_type  = grid
rlist  = 1.0  ; cut-off distance for short range neighbors
rcoulomb = 1.0  ; distance for coulomb cut-off
coulombtype = PME  ; electrostatics (Particle Mesh Ewald method)
fourierspacing = 0.12  ; max grid spacing when using PPPM or PME
vdw-type = Shift
rvdw  = 1.0  ; VDW cut-off
Tcoupl  = no  ; temperature coupling
Pcoupl  = no  ; pressure coupling
gen_vel  = no
```

#### Run `grompp`

```
gmx grompp -f mdpfile -o output.tpr -p topology_file -c input_str -maxwarn 50
```

### Run em

```
gmx mdrun -v -deffnm em -nb gpu
```

## NVT

### Generate `.mdp` file

```
title                   = OPLS Lysozyme NVT
define                  = -DPOSRES  ; position restrain the protein

; Run parameters
integrator              = md        ; leap-frog integrator
nsteps                  = 400000      ; 2 * 50000 = 100 ps
dt                      = 0.001     ; 2 fs

; Output control
nstxout                 = 5000       ; save coordinates every 1.0 ps 9.999
nstvout                 = 5000       ; save velocities every 1.0 ps
nstenergy               = 5000      ; save energies every 1.0 ps
nstlog                  = 5000       ; update log file every 1.0 ps

; Bond parameters
continuation            = no        ; first dynamics run
constraint_algorithm    = lincs     ; holonomic constraints 
constraints             = h-bonds   ; bonds involving H are constrained
lincs_iter              = 1         ; accuracy of LINCS
lincs_order             = 4         ; also related to accuracy

; Nonbonded settings 
cutoff-scheme           = Verlet    ; Buffered neighbor searching
ns_type                 = grid      ; search neighboring grid cells
nstlist                 = 10        ; 20 fs, largely irrelevant with Verlet
rcoulomb                = 1.0       ; short-range electrostatic cutoff (in nm)
rvdw                    = 1.0       ; short-range van der Waals cutoff (in nm)
DispCorr                = EnerPres  ; account for cut-off vdW scheme

; Electrostatics
coulombtype             = PME       ; Particle Mesh Ewald for long-range electrostatics
pme_order               = 4         ; cubic interpolation
fourierspacing          = 0.16      ; grid spacing for FFT

; Temperature coupling is on
Tcoupl      = V-rescale
tc_grps     = system 
tau_t       = 0.1
ref_t       = 333

; Pressure coupling is off
pcoupl                  = no        ; no pressure coupling in NVT

; Periodic boundary conditions
pbc                     = xyz       ; 3-D PBC
periodic-molecules = yes

gen_vel   = yes
gen_temp  = 300
gen_seed  = -1
```

### Run md

```
gmx grompp -f mdpfile -o outputfile -p topologyfile -c inputstructure
gmx mdrun -v -deffnm mdname
```

## NPT

### Generate `.mdp` file

```
title                   = OPLS Lysozyme NVT equilibration 
define                  = -DPOSRES  ; position restrain the protein
; Run parameters
integrator              = md        ; leap-frog integrator
nsteps                  = 2500000    ; 2 * 50000 = 100 ps
dt                      = 0.001     ; 2 fs
; Output control
nstxout                 = 10000       ; save coordinates every 1.0 ps 102
nstvout                 = 10000       ; save velocities every 1.0 ps
nstenergy               = 10000      ; save energies every 1.0 ps
nstlog                  = 10000       ; update log file every 1.0 ps
; Bond parameters
continuation            = yes        ; first dynamics run
constraint_algorithm    = lincs     ; holonomic constraints 
constraints             = h-bonds   ; bonds involving H are constrained
lincs_iter              = 1         ; accuracy of LINCS
lincs_order             = 4         ; also related to accuracy
; Nonbonded settings 
cutoff-scheme           = Verlet    ; Buffered neighbor searching
ns_type                 = grid      ; search neighboring grid cells
nstlist                 = 10        ; 20 fs, largely irrelevant with Verlet
rcoulomb                = 1.0       ; short-range electrostatic cutoff (in nm)
rvdw                    = 1.0       ; short-range van der Waals cutoff (in nm)
DispCorr                = EnerPres  ; account for cut-off vdW scheme
; Electrostatics
coulombtype             = PME       ; Particle Mesh Ewald for long-range electrostatics
pme_order               = 4         ; cubic interpolation
fourierspacing          = 0.16      ; grid spacing for FFT
; Temperature coupling is on
Tcoupl      = V-rescale
tc_grps     = system 
tau_t       = 0.1
ref_t       = 333 ; temperature
; Pressure coupling is on
Pcoupl          = Berendsen
pcoupltype      = isotropic
tau_p           = 1.0       
compressibility = 1.1e-4 
ref_p           = 1.0              ;pressure
refcoord_scaling = com

; Periodic boundary conditions
pbc                     = xyz       ; 2-D PBC
periodic-molecules = yes

; Velocity generation
gen_vel                 = no        ; assign velocities from Maxwell distribution
gen_temp                = 300       ; temperature for Maxwell distribution
gen_seed                = -1        ; generate a random seed
```

### Run md

```
gmx grompp -f mdpfile -o outputfile -p topologyfile -c inputstructure
gmx mdrun -v -deffnm mdname
```
