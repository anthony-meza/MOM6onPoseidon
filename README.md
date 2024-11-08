| MOM6 source repo | MOM6 documentation | MOM6 coverage |
|:----------------:|:------------------:|:-------------:|
| [![Regression](https://github.com/NOAA-GFDL/MOM6/actions/workflows/regression.yml/badge.svg)](https://github.com/NOAA-GFDL/MOM6/actions/workflows/regression.yml) | [![Read The Docs Status](https://readthedocs.org/projects/mom6/badge/?version=main)](https://mom6.readthedocs.io/en/main/?badge=main) | [![codecov](https://codecov.io/gh/NOAA-GFDL/MOM6/branch/dev/gfdl/graph/badge.svg?token=uF8SVydCdp)](https://codecov.io/gh/NOAA-GFDL/MOM6) |

# Building and compiling FMS and MOM6 on Poseidon @ WHOI

## Step 0: download repository and change directories

```
git clone git@github.com:anthony-meza/MOM6onPoseidon.git

cd MOM6onPoseidon
```


## Step 1: build fms

```
module load gcc/9.3.1 
module load openmpi/gcc/3.0.1
module load netcdf/gcc9

rm -r build/fms
mkdir -p build/fms/
(cd build/fms/; rm -f path_names; \
../../src/mkmf/bin/list_paths -l ../../src/FMS; \
../../src/mkmf/bin/mkmf -t ../../src/mkmf/templates/linux-ubuntu-xenial-gnu.mk -p libfms.a -c "-Duse_libMPI -Duse_netCDF -DSPMD" path_names)

(cd build/fms/; make NETCDF=3 REPRO=1 libfms.a -j)
```
## Step 2: build ice-ocean-SIS2

```
module load gcc/9.3.1
module load openmpi/gcc/3.0.1
module load netcdf/gcc9

mkdir -p build/ice_ocean_SIS2/
(cd build/ice_ocean_SIS2/; rm -f path_names; \
../../src/mkmf/bin/list_paths -l ./ ../../src/MOM6/config_src/{infra/FMS1,memory/dynamic_symmetric,drivers/FMS_cap,external} ../../src/SIS2/config_src/dynamic_symmetric ../../src/MOM6/src/{*,*/*}/ ../../src/{atmos_null,coupler,land_null,ice_param,icebergs/src,SIS2,FMS/coupler,FMS/include}/)
(cd build/ice_ocean_SIS2/; \
../../src/mkmf/bin/mkmf -t ../../src/mkmf/templates/linux-ubuntu-xenial-gnu.mk -o '-I../fms' -p MOM6 -l '-L../fms -lfms' -c '-Duse_AM3_physics -D_USE_LEGACY_LAND_' path_names )

(cd build/ice_ocean_SIS2/;  make REPRO=1 MOM6 -j)
```

## Step 3: download OM_1deg files

Access necessary configuration and forcing files from this link [ftp link](ftp.gfdl.noaa.gov/perm/Alistair.Adcroft/MOM6-testing). Copy files to ```ice_ocean_SIS2/OM_1deg```


## Step 4: run ice-ocean-SIS2

```
module load gcc/9.3.1
module load openmpi/gcc/3.0.1
module load netcdf/gcc9

cd ice_ocean_SIS2/OM_1deg
mpirun -np N ../../build/ice_ocean_SIS2/MOM6 #N = number of processors 
```

# MOM6-examples

This repository provides the configurations (input parameters and data) and their corresponding
regression data (for testing), of models that involve [MOM6](https://github.com/NOAA-GFDL/MOM6)
and [SIS2](https://github.com/NOAA-GFDL/SIS2). The repository also contains tools
for analysis and preprocessing.

# Where to find information

To find information, start at the
[MOM6-examples wiki](https://github.com/NOAA-GFDL/MOM6-examples/wiki).

For general inquiries about using MOM6 and affiliated models, use the
[CESM MOM6 forums](https://bb.cgd.ucar.edu/cesm/forums/mom6.148/).

Requests for help and other issues associated with the tools or configurations
should be registered at
[MOM6-examples issues](https://github.com/NOAA-GFDL/MOM6-examples/issues).

Issues specific to the MOM6 source code should be registered at
[MOM6 issues](https://github.com/NOAA-GFDL/MOM6/issues).

Issues specific to the SIS2 source code should be registered at
[SIS2 issues](https://github.com/NOAA-GFDL/SIS2/issues).

# What files are what

The top level directory structure groups source code and input files as follow:

| File/directory              | Purpose |
| --------------              | ------- |
| ```LICENSE.md```            | a copy of the Gnu lesser general public license, version 3. |
| ```README.md```             | this file with basic pointers to more information. |
| ```src/```                  | source code for MOM6, SIS2 and FMS-shared code. |
| ```tools/```                | tools for working with MOM6 (not source code and not necessarily supported). |
| ```ocean_only```            | experiments that just use MOM6 |
| ```ice_ocean_SIS```         | experiments that use MOM6 and SIS code in coupled mode |
| ```ice_ocean_SIS2```        | experiments that use MOM6 and SIS2 code in coupled mode |
| ```coupled_AM2_LM2_SIS```   | experiments that use MOM6, SIS, LM2 and AM2 code, ie. fully coupled |
| ```coupled_AM2_LM3_SIS/```  | experiments that use MOM6, SIS, LM3 and AM2 code, ie. fully coupled |
| ```coupled_AM2_LM3_SIS2/``` | experiments that use MOM6, SIS2, ML3 and AM2 code, ie. fully coupled |


| Directory            | Purpose |
| ---------            | ------- |
| ```src/MOM6/```      | is a git submodule that contains the source code for MOM6 |
| ```src/SIS2/```      | is a git submodule that contains the source code for SIS2 |
| ```src/FMS/```       | is a git submodule that contains the source code for FMS |

# Policies

The repository policies (repository access, branches, procedures, ...) are the same as the
[MOM6 source code policies](https://github.com/NOAA-GFDL/MOM6-examples/wiki/MOM6-repository-policies).

# Disclaimer

The United States Department of Commerce (DOC) GitHub project code is provided 
on an ‘as is’ basis and the user assumes responsibility for its use. DOC has
relinquished control of the information and no longer has responsibility to
protect the integrity, confidentiality, or availability of the information. Any
claims against the Department of Commerce stemming from the use of its GitHub
project will be governed by all applicable Federal law. Any reference to
specific commercial products, processes, or services by service mark,
trademark, manufacturer, or otherwise, does not constitute or imply their
endorsement, recommendation or favoring by the Department of Commerce. The
Department of Commerce seal and logo, or the seal and logo of a DOC bureau,
shall not be used in any manner to imply endorsement of any commercial product
or activity by DOC or the United States Government.

This project code is made available through GitHub but is managed by NOAA-GFDL
at https://gitlab.gfdl.noaa.gov.
