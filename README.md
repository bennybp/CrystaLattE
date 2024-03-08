# <img align="center" src="https://github.com/carlosborca/CrystaLattE/blob/master/media/logo/Logo.png" height=260>

Automated calculation of crystal lattice energies with the many-body expansion.

| Category | Badges |
|-------------|-------------|
| **Status** | [![Travis Build Status](https://travis-ci.com/carlosborca/CrystaLattE.svg?branch=master)](https://travis-ci.org/carlosborca/CrystaLattE) [![codecov](https://codecov.io/gh/carlosborca/CrystaLattE/branch/master/graph/badge.svg)](https://codecov.io/gh/carlosborca/CrystaLattE/branch/master) [![Total alerts](https://img.shields.io/lgtm/alerts/g/carlosborca/CrystaLattE.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/carlosborca/CrystaLattE/alerts/) [![Language grade: Python](https://img.shields.io/lgtm/grade/python/g/carlosborca/CrystaLattE.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/carlosborca/CrystaLattE/context:python) |
| **Foundation** | [![License](https://img.shields.io/github/license/carlosborca/CrystaLattE.svg)](https://opensource.org/licenses/LGPL-3.0) [![GitHub Top Languages](https://img.shields.io/github/languages/top/carlosborca/CrystaLattE)](https://github.com/carlosborca/CrystaLattE/) |
| **GitHub Info** | [![GitHub Code Size](https://img.shields.io/github/languages/code-size/carlosborca/CrystaLattE)](https://github.com/carlosborca/CrystaLattE/) [![GitHub Commits per Month](https://img.shields.io/github/commit-activity/m/carlosborca/CrystaLattE)](https://github.com/carlosborca/CrystaLattE/) [![GitHub Last Commit](https://img.shields.io/github/last-commit/carlosborca/CrystaLattE)](https://github.com/carlosborca/CrystaLattE/) |
| **Citation** | [![doi](https://img.shields.io/badge/DOI-10.1063%2F1.5120520-blue)](http://dx.doi.org/10.1063/1.5120520) |

## Overview

CrystaLattE is a software that automates the computation of crystal lattice energies using the many-body cluster expansion. The required computations on dimers, trimers, etc., within the crystal are independent of each other, leading to a naturally parallel approach. The algorithm exploits the long-range three-dimensional periodic order of crystals to automatically detect and avoid redundant or unnecessary computations.

## General Information

CrystaLattE has an interface with the quantum chemistry package PSI4. To run, the code requires a crystallographic information file containing structural information of the crystal and an input file specifying execution details. Work continues in the creation of a CrystaLattE `pip` package. So, for the moment, the instructions to download and install CrystaLattE and to create a _conda environment_ that includes PSI4 are presented below. 

### Installation

Minimal set of commands to install CrystaLattE on Linux, MacOS, or Windows (with the Windows Subsystem for Linux). Last tested on 4 October 2019:

#### 1. Install Miniconda:

If you have an installation of _Conda_ in your system, please skip to step 2. Otherwise, _Miniconda_ is required and the installer is available from the the Anaconda website. To download the installer from the terminal (in Linux or the Windows Subsystem for Linux): 

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

(_Note_) If you use MacOS, replace `Linux` by `MacOSX` on the previous command.

Run the installer following the on-screen instructions:

```
bash Miniconda3-latest-Linux-x86_64.sh
```

After the installation is complete, close the terminal and start a new shell.

(_Optional_) Disable automatic activation of the _base_ conda environment:

```
conda config --set auto_activate_base false
```

#### 2. Create a _Conda Environment_ for CrystaLattE

CrystaLattE requires PSI4 and PyCIFRW. Conda offers the possibility of creating an _environment_ that contains all the dependencies required by CrystaLattE. To download and install PSI4 and other related software tools in a new _cle_ environment execute the command below and follow the on-screen instructions:

```
conda create -n cle python psi4 pycifrw -c conda-forge
```

#### 3. Activate the _cle_ environment

To use the recently created _cle_ environment, activate it:

```
conda activate cle
```

#### 4. Clone CrystaLattE from its GitHub repository:

In you file system navigate to the location where you would like to place the root directory of CrystaLattE and clone it from its corresponding GitHub repository:

```
git clone https://github.com/carlosborca/CrystaLattE.git
```

#### 5. Test CrystaLattE

Go to the root directory of the CrystaLatte repository in the file system and run the test suite:

```
pytest
```

(_Note_) It is known that the `v2rdm_casscf` plugin of PSI4 may cause execution errors when trying to import PSI4 from CrystaLattE. If such error is encountered during testing, remove it.

```
conda remove v2rdm_casscf
```

### How to run CrystaLattE

CrystaLattE requires a crystallographic information file (.cif) and an options input file (.cle). CIF files can be obtained from multiple sources. For example, from the Cambridge Crystallographic Data Centre (CCDC) website. The options file must be generated by the user. A template is presented below:

#### Preparing the options input

Example of an options input file for CrystaLattE.

```
# This is a typical CrystaLattE input template: input.cle

# Blank lines and lines starting with the hash character are omitted.
# Padding blank spaces are also ignored.

# The format is:
# Keywords  equal  Value

cif_input       =  ../MyCrystals/OneCrystal.cif
cif_output      =  ../MyCrystals/SuperCell.xyz
cif_a           =  5
cif_b           =  5
cif_c           =  5
bfs_thresh      =  1.2
uniq_filter     =  ChSEV
nmers_up_to     =  3
r_cut_com       =  12.0
r_cut_monomer   =  15.0
r_cut_dimer     =  9.0
r_cut_trimer    =  12.0
r_cut_tetramer  =  6.0
r_cut_pentamer  =  4.0
cle_run_type    =  psi4api + quiet
psi4_method     =  MP2/aug-cc-pV[TQ]Z + D:FNO-CCSD(T)/aug-cc-pVDZ
psi4_bsse       =  cp
psi4_memory     =  8 GB
verbose         =  2
```

#### Keywords

Description of keywords and their acceptable values.

| Keyword          | Acceptable Values                              | Default      | Example      | Notes                |
|------------------|------------------------------------------------|--------------|--------------|----------------------|
| `cif_input`      | \<RelativePath\>\/\<FileName\>.cif             | *No default* | Benzene.cif  | Must be a .cif       |
| `cif_output`     | \<RelativePath\>\/\<FileName\>.xyz             | sc.xyz       | Benzene.xyz  | Must be a .xyz       |
| `cif_a`          | Odd positive integer                           | 5            | 9            |                      |
| `cif_b`          | Odd positive integer                           | 5            | 7            |                      |
| `cif_c`          | Odd positive integer                           | 5            | 11           |                      |
| `bfs_thresh`     | Positive float                                 | 1.2          | 1.3          | vdW radii multiplier |
| `uniq_filter`    | ChSEV, Dreamaligner                            | ChSEV        | Dreamaligner |                      |
| `nmers_up_to`    | 2, 3, 4, 5                                     | 2            | 3            | Dimers, Trimers...   |
| `r_cut_com`      | Positive float                                 | 10.0         | 12.0         | Angstroms            |
| `r_cut_monomer`  | Positive float                                 | 12.0         | 15.0         | Angstroms            |
| `r_cut_dimer`    | Positive float                                 | 10.0         | 8.0          | Angstroms            |
| `r_cut_trimer`   | Positive float                                 | 8.0          | 10.0         | Angstroms            |
| `r_cut_tetramer` | Positive float                                 | 6.0          | 5.0          | Angstroms            |
| `r_cut_pentamer` | Positive float                                 | 4.0          | 3.0          | Angstroms            |
| `cle_run_type`   | psi4api, psithon, makefp, test, quiet, timings | psi4api      | test + quiet | Separate with +      |
| `psi4_method`    | String                                         | HF/STO-3G    | HF-3c        | See PSI4 manual      |
| `psi4_bsse`      | vmfc, cp, nocp                                 | cp           | nocp         |                      |
| `psi4_memory`    | String                                         | 500 MB       | 2 GB         |                      |
| `verbose`        | 0, 1, 2                                        | 1            | 2            |                      |

#### Running CrystaLattE

Finally, to execute the code:

```
./crystalatte.py input.cle
```

#### Known Issues

Precision issues may arise when computing a large number of structures at default energy and density convergence criteria if using Psi4 1.3.2 and newer versions. Although the default energy and density convergence criteria for Psi4 calculations has been increased in CrystaLattE, the user should be careful.

#### Copyright

Copyright (c) 2020, Carlos H. Borca


#### Acknowledgements
 
Project based on the 
[Computational Molecular Science Python Cookiecutter](https://github.com/molssi/cookiecutter-cms) version 1.0.
