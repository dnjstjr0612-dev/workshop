# NEMO5–XIOS3_260729.pptx — extracted text

※ 각 슬라이드의 일반 텍스트 객체와 이미지 안의 OCR 텍스트를 함께 모았습니다. OCR 특성상 스크린샷/작은 글씨에는 오인식이 있을 수 있습니다.

## Slide 1
### Text objects
tar -xzf /data4/NEMO/nemo_500_env.tar.gz -C ${HOME}/.conda/envs/nemo_500_env

conda activate ${HOME}/.conda/envs/nemo_500_env

conda-unpack

conda deactivate

mkdir –p ~/nemo5.0-xios3

cp –r /data4/NEMO/xios_3 /data4/NEMO/nemo_5.0.0 ~/nemo5.0-xios3

NEMO5–XIOS3 실습 환경 설치

## Slide 2
### Text objects
~/.zshrc (or ~/.bashrc) 맨 아래에 추가

nemo_activate() {
    conda activate nemo_500_env || return 1

    unset CC CXX FC F77 F90
    unset NETCDF_HOME HDF5_HOME BOOST_HOME
    unset NETCDF_LIBDIR HDF5_LIBDIR MPI_LIBDIR
    unset NETCDF_LIB HDF5_LIB MPI_LIB
    unset OASIS_INCDIR OASIS_LIBDIR OASIS_LIB
    unset WORKDIR SETTE_NEMO_VALIDATION_DIR

    export PATH="${CONDA_PREFIX}/bin:${PATH}"

    hash -r 2>/dev/null || true
    rehash 2>/dev/null || true

    export NEMO_HOME="${HOME}/nemo5.0-xios3"
    export XIOS_HOME="${NEMO_HOME}/xios_3"
    export OASIS_HOME="/data4/NEMO/oasis3-mct"

    echo "CONDA_PREFIX=${CONDA_PREFIX}"
    echo "NEMO_HOME=${NEMO_HOME}"
    echo "XIOS_HOME=${XIOS_HOME}"
    echo "OASIS_HOME=${OASIS_HOME}"

    echo
    echo "Compiler and library commands:"
    echo "  mpicc     = $(command -v mpicc)"
    echo "  mpif90    = $(command -v mpif90)"
    echo "  mpirun    = $(command -v mpirun)"
    echo "  nc-config = $(command -v nc-config)"
    echo "  nf-config = $(command -v nf-config)"
    echo "  h5pcc     = $(command -v h5pcc)"

    echo
    echo "netCDF version  : $(nc-config --version)"
    echo "netCDF prefix   : $(nc-config --prefix)"
    echo "Parallel NetCDF : $(nc-config --has-parallel4)"
}

NEMO5–XIOS3 실습 환경 설치

[참고] 환경셀 종류 확인

 ~/.bashrc 수정

 ~/.zshrc 수정

### Image OCR
[Image 1]
(base) EEE) echo ${SHELL}
/usr/bin/zsh

[Image 2]
(base) [jhyun@acc@00 ~]$ echo ${SHELL}
/bin/bash

## Slide 3
### Text objects
source ~/.zshrc (or ~/.bashrc)

nemo_activate 설정 확인

NEMO5–XIOS3 실습 환경 설치

### Image OCR
[Image 1]
(base) eR: source ~/.zshrc

(base) nemo_activate

CONDA_PREFIX=/home/jhyun/.conda/envs/nemo_500_env

NEMO_HOME=/home/jhyun/nemo5.0-xios3

XIO0S_HOME=/home/jhyun/nemo5.0-xios3/xios_3

OASIS_HOME=/data4/NEMO/oasis3-mct

Compiler and library commands:
mpicc = /home/jhyun/.conda/envs/nemo_500_env/bin/mpicc
mpif90 = /home/jhyun/.conda/envs/nemo_500_env/bin/mpif90
mpirun = /home/jhyun/.conda/envs/nemo_500_env/bin/mpirun
nc-config = /home/jhyun/.conda/envs/nemo_500_env/bin/nc-config
nf-config = /home/jhyun/.conda/envs/nemo_500_env/bin/nf-config
h5pcc = /home/jhyun/.conda/envs/nemo_500_env/bin/h5pcc

netCDF version : netCDF 4.9.2

netCDF prefix : /home/jhyun/.conda/envs/nemo_500_env

Parallel NetCDF : yes

## Slide 4
### Text objects
cd ${XIOS_HOME}
./make_xios --full --prod --arch conda --job 16

NEMO5–XIOS3 실습 환경 설치

### Image OCR
[Image 1]
X ssh ©)
r_distribution_description.o server_from_client_source_filter.o server_to_client_store_filter.o eo 6:19:31 PM
service_node.o services.o services_manager.o sum_reduction.o symbols_core.o symbols_with_addr2line.o s¥me

ghelp.o symbols_with_dl.o symbols_with_libbacktrace.o symbols_with_libdwarf.o symbols_with_nothing.o temp

.0 temporal_splitting.o temporal_splitting_interface.o temporal_splitting_interface_attr.o temporal_trans

.0 ternary_arithmetic_filter.o thread_manager.o timer.o timer_interface.o timerremap.o token_manager.o tr
sfer_transform_connector.o transform_connector.o transform_filter.o transformation_path.o tree.o triple.o

o unary_arithmetic_filter.o unwind_with_execinfo.o unwind_with_nothing.o unwind_with_unwind.o unwind_with
ser_defined.o uuid.o variable.o variable_interface.o variable_interface_attr.o variablegroup_interface.o
up_interface_attr.o weight_transform_connector.o window_base.o window_manager.o workflow_graph.o xios.o x

ces.o xml_node.o xml_parser.o xml_parser_decl.o yacc_parser.o zoom_axis.o zoom_axis_interface.o zoom_axis

attr.o zoom_domain.o zoom_domain_interface.o zoom_domain_interface_attr.o

ar: creating /home/jhyun/nemo5.0-xios3/xios_3/tmp/lib__fcm__generic_testcase.a

ar: creating /home/jhyun/nemo5.@-xios3/xios_3/tmp/lib__fcm__xios_server.a

ar: creating /home/jhyun/nemo5.0-xios3/xios_3/tmp/libxios.a

/home/jhyun/.conda/envs/nemo_500_env/bin/mpif9@ -o xios_server.exe /home/jhyun/nemo5.@-xios3/xios_3/obj/se6:19:32 PM

o -L/home/jhyun/nemo5.@-xios3/xios_3/lib -l__fcm__xios_server -L/home/jhyun/.conda/envs/nemo_asmlys/lib -

netcdf -lhdf5_hl -lhdf5 -lmpi -lstdc++ -lstdc++ -lpthread

/home/jhyun/.conda/envs/nemo_500_env/bin/mpif9@ -o generic_testcase.exe /home/jhyun/nemo5.0-xios3/xios_3/

_testcase.o -L/home/jhyun/nemo5.0-xios3/xios_3/lib -1__fcm__generic_testcase -L/home/jhyun/.conda/envs/ne

ib -lnetcdff -lnetcdf -lhdf5_hl -lhdf5 -lmpi -lstdc++ -lstdc++ -lpthread

->Make: 163 seconds 6:19:36 PM
->TOTAL: 182 seconds

Build command finished on Mon Jul 27 18:19:35 2026. a

## Slide 5
### Text objects
NEMO5–XIOS3 입문 워크숍
모델 구조의 이해와 기본 예제 실습하기

Group Meeting
July 29th 2026

## Slide 6
### Text objects
A numerical modelling framework for ocean and climate research and forecasting
Simulates ocean dynamics, sea ice, and marine biogeochemistry
Applicable across a wide range of spatial and temporal scales
Used for:
fundamental ocean research
operational oceanography
seasonal forecasting
climate studies and projections

What is NEMO?
* NEMO: Nucleus for European Modelling of the Ocean

### Image OCR
[Image 1]
Community ocean model for
multifarious space and time scales
Click on NEMO icon to go back to

NEMO Gitlab
5.0.0

## Slide 7
### Text objects
Heuzé et al. 2021

### Image OCR
[Image 1]
Table 1. The 35 CMIP6 models used in this study; their ocean component; horizontal resolution in ° latitude x ° longitude; vertical grid type
(p means isopycnic, o terrain-following, several symbols a hybrid grid) and number of vertical levels; and official reference. N/A indicates
that no paper has been published yet for the CMIP6 configuration.
Model name Ocean component Horizontal Vertical Reference
ESS-CM’ MOM. x Z Ni
2 ACCESS-ESM1-5 MOMS 1x1 z* 50 Ziehn et al. (2017)
3 BCC-CSM2-MR MOM4-L40 1x1 z 40 Wu et al. (2019)
4 BCC-ESM1 MOM4-L40 1x1 z 40 Wu et al. (2019)
5 CAMS-CSMI1-0 MOM4 1x1 z 50 Rong et al. (2019)
6 CESM2 POP2 1x1 z 60 Danabasoglu et al. (2020)
7  CESM2-FV2 POP2 1x1 z 60 Danabasoglu et al. (2020)
8 CESM2-WACCM POP2 1x1 z 60 Danabasoglu et al. (2020)
9 CESM2-WACCM-FV2 POP2 1x1 z 60 Danabasoglu et al. (2020)
10 CNRM-CM6-1 NEMO3.6 1x1 z* 75 Voldoire et al. (2019)
11 CNRM-ESM2-1 NEMO3.6 1x1 z* 75 Séférian et al. (2019)
12 CanESMS NEMO3.4.1 1x1 z 45 Swart et al. (2019)
13  EC-Earth3 NEMO3.6 1x1 z* 75 N/A
14 EC-Earth3-Veg NEMO3.6 1x1 z* 75 N/A
15  GFDL-CM4 MOM6 0.25x0.25  p-z*75 Held et al. (2019)
16 GFDL-ESM4 MOM6 0.5 x 0.5 p-z* 75 N/A
17 GISS-E2-1-G GISS Ocean 1.25x1 z 40 N/A
18 GISS-E2-1-G-CC GISS Ocean 1.25x1 z 40 N/A
19 GISS-E2-1-H HYCOM 1x1 zZ-p-o 32 N/A
20 HadGEM3-GC31-LL NEMO-HadGEM3-GO6.0 1x1 z* 75 Kuhlbrodt et al. (2018)
21 INM-CM5-0 INM-OM5 0.5 x 0.25 0 40 Volodin and Gritsun (2018)
22 IPSL-CM6A-LR NEMO3.6 1x1 z* 75 Lurton et al. (2020)
23 MCM-UA-1-0 MOM1 2x2 z 18 N/A
24 MIROC-ES2L COCO4.9 1x1 z-0 62 Hajima et al. (2020)
25 MIROC6 COC04.9 1x1 z-o 62 Tatebe et al. (2019)
26 MPI-ESM-1-2-HAM MPIOM1.6.3 1.5 x 1.5 z 40 Mauritsen et al. (2019)
27 MPI-ESM1-2-HR MPIOM1.6.3 0.4x 0.4 z 40 Miiller et al. (2018)
28 MPI-ESM1-2-LR MPIOM1.6.3 1.5x 1.5 z 40 Mauritsen et al. (2019)
29 MRI-ESM2-0 MRI.COM4.4 1 x 0.5 z* 60 Yukimoto et al. (2019)
30 NESM3 NEMO3.4 1x1 z 46 Cao et al. (2018)
31 NorCPM1 MICOM 1x1 z- p53 Counillon et al. (2016)
32 NorESM2-LM MICOM 1x1 z- p53 Tjiputra et al. (2020)
33 NorESM2-MM MICOM 1x1 z- p53 Tjiputra et al. (2020)
34 SAMO-UNICON POP2 1x1 z 60 Park et al. (2019)
35 UKESM1-0-LL NEMO-HadGEM3-GO6.0 1x1 z* 75 Sellar et al. (2020)

## Slide 8
### Text objects
What does NEMO simulate?
ocean dynamics, sea ice, and marine biogeochemistry

ICE
sea ICE

OCEOCEan physics

TOP
Tracers in the Ocean Paradigm

Components are selected and combined according to the model configuration.

AGE

CFC

C14

PISCES
Biogeochemistry

## Slide 9
### Text objects
NEMO reference configurations
ex) Global ORCA2 reference configurations

Reference configuration | Ocean physics | Sea ice | Tracers / BGC
ORCA2_ICE_PISCES | Online OCE | SI3 | TOP–PISCES
ORCA2_OFF_PISCES | Prescribed physical fields | - | TOP–PISCES
ORCA2_OFF_TRC | Prescribed physical fields | - | TOP tracers

### Image OCR
[Image 1]
(nemo_500_env) 2, nemo_5@@_env ~~ ls -altr ${NEMO_HOME}/nemo_5.0.0/cfgs/
total 20

drwxrwxr-x 3 jhyun jhyun 51 Jul 27 13:54 AMM12

drwxrwxr-x 3 jhyun jhyun 56 Jul 27 13:54 AGRIF_DEMO
drwxrwxr-x 3 jhyun jhyun 57 Jul 27 13:54 GYRE_PISCES
drwxrwxr-x 3 jhyun jhyun 74 Jul 27 13:54 GYRE_BFM

drwxrwxr-x 9 jhyun jhyun 4096 Jul 27 13:54 C1D

drwxrwxr-x 3 jhyun jhyun 62 Jul 27 13:54 ORCA2_OFF_PISCES
drwxrwxr-x 4 jhyun jhyun 84 Jul 27 13:54 ORCA2_ICE_PISCES
drwxrwxr-x 3 jhyun jhyun 59 Jul 27 13:54 ORCA2_SAS_ICE
drwxrwxr-x 3 jhyun jhyun 59 Jul 27 13:54 ORCA2_OFF_TRC
drwxrwxr-x 3 jhyun jhyun 53 Jul 27 13:54 SPITZ12

drwxrwxr-x 2 jhyun jhyun 4096 Jul 27 13:54 SHARED

drwxrwxr-x 3 jhyun jhyun 52 Jul 27 13:54 WED@25

drwxrwxr-x 3 jhyun jhyun 65 Jul 27 13:54 X3_ORCA2_ICE_PISCES
-rw-rw-r-- 1 jhyun jhyun 243 Jul 27 13:54 ref_cfgs.txt

## Slide 10
### Text objects
What is XIOS?
* XIOS: XML Input Output Server

NEMO

XIOS

NetCDF출력

OCE · SI3 · TOP/PISCES
매 time step의 모델 변수를 계산

변수 선택
시간 평균·최대·최소
수직 적분·영역 연산
파일 분할·병렬 쓰기

(예) 월평균 해수면 온도
일최대 해빙 농도
0–300 m ocean heat content

## Slide 11
### Text objects
NEMO5–XIOS3

# NEMO와 XIOS는 병렬 파일 입출력이 필수적

nc-config --all
h5pcc -showconfig

### Image OCR
[Image 1]
System prerequisites
The following should be provided natively by your system, if not, they need to be installed
from the official repositories:

e You need a Unix-like machine (e.g. Linux Distributions, MacOS)

e subversion (svn) for version control of XIOS sources

e git for version control of NEMO sources

e Perl interpreter

e Fortran compiler ( ifort , gfortran, pgfortran, ftn,...),

e Message Passing Interface (MPI) implementation (e.g. OpenMP! or MPICH).

e Network Common Data Form (NetCDF) library with its underlying Hierarchical Data Form

(HDF)

By default, NEMO requires MPI-3. However, it is possible to circumnavigate this by
using the following work-arounds:

e Activate the key_mpi2 preprocessor key at compile time. This will allow you to run
the model using MPI-2, but keep in mind that you will lose some performance
benefits.

e Activate the key_mpi_off preprocessor key at compile time. This will allow you to
run the model only on a single process (no MPI parallelization) and you will not be
able to use XIOS.

## Slide 12
### Image OCR
[Image 1]
conda create -n nemo_asmlys python=3.9
conda activate nemo_asmlys
o Install build tools
conda install -c conda-forge cmake
conda install -c conda-forge make
conda install -c conda-forge compilers
oO Install libraries (HDF5/NetCDF with MPI)
conda install -c conda-forge “hdf5=*=mpi*”
conda install -c conda-forge "libnetcdf=*=mpi_*"
conda install -c conda-forge “netcdf-fortran=*=mpi_*"
conda install libxml2 libxsit
oO Install version control & Perl
conda install -c conda-forge subversion git perl
conda install -c conda-forge perl-uri
conda install -c conda-forge perl-xml-parser perl-uri perl-io-stringy

## Slide 13
### Text objects
tar -xzf /data4/NEMO/nemo_500_env.tar.gz -C ${HOME}/.conda/envs/nemo_500_env

conda activate ${HOME}/.conda/envs/nemo_500_env

conda-unpack

conda deactivate

mkdir –p ~/nemo5.0-xios3

cp –r /data4/NEMO/xios_3 /data4/NEMO/nemo_5.0.0 ~/nemo5.0-xios3

NEMO5–XIOS3 실습 환경 설치

## Slide 14
### Text objects
~/.zshrc (or ~/.bashrc) 맨 아래에 추가

nemo_activate() {
    conda activate nemo_500_env || return 1

    unset CC CXX FC F77 F90
    unset NETCDF_HOME HDF5_HOME BOOST_HOME
    unset NETCDF_LIBDIR HDF5_LIBDIR MPI_LIBDIR
    unset NETCDF_LIB HDF5_LIB MPI_LIB
    unset OASIS_INCDIR OASIS_LIBDIR OASIS_LIB
    unset WORKDIR SETTE_NEMO_VALIDATION_DIR

    export PATH="${CONDA_PREFIX}/bin:${PATH}"

    hash -r 2>/dev/null || true
    rehash 2>/dev/null || true

    export NEMO_HOME="${HOME}/nemo5.0-xios3"
    export XIOS_HOME="${NEMO_HOME}/xios_3"
    export OASIS_HOME="/data4/NEMO/oasis3-mct"

    echo "CONDA_PREFIX=${CONDA_PREFIX}"
    echo "NEMO_HOME=${NEMO_HOME}"
    echo "XIOS_HOME=${XIOS_HOME}"
    echo "OASIS_HOME=${OASIS_HOME}"

    echo
    echo "Compiler and library commands:"
    echo "  mpicc     = $(command -v mpicc)"
    echo "  mpif90    = $(command -v mpif90)"
    echo "  mpirun    = $(command -v mpirun)"
    echo "  nc-config = $(command -v nc-config)"
    echo "  nf-config = $(command -v nf-config)"
    echo "  h5pcc     = $(command -v h5pcc)"

    echo
    echo "netCDF version  : $(nc-config --version)"
    echo "netCDF prefix   : $(nc-config --prefix)"
    echo "Parallel NetCDF : $(nc-config --has-parallel4)"
}

NEMO5–XIOS3 실습 환경 설치

[참고] 환경셀 종류 확인

 ~/.bashrc 수정

 ~/.zshrc 수정

### Image OCR
[Image 1]
(base) EEE) echo ${SHELL}
/usr/bin/zsh

[Image 2]
(base) [jhyun@acc@00 ~]$ echo ${SHELL}
/bin/bash

## Slide 15
### Text objects
source ~/.zshrc (or ~/.bashrc)

nemo_activate 설정 확인

NEMO5–XIOS3 실습 환경 설치

### Image OCR
[Image 1]
(base) eR: source ~/.zshrc

(base) nemo_activate

CONDA_PREFIX=/home/jhyun/.conda/envs/nemo_500_env

NEMO_HOME=/home/jhyun/nemo5.0-xios3

XIO0S_HOME=/home/jhyun/nemo5.0-xios3/xios_3

OASIS_HOME=/data4/NEMO/oasis3-mct

Compiler and library commands:
mpicc = /home/jhyun/.conda/envs/nemo_500_env/bin/mpicc
mpif90 = /home/jhyun/.conda/envs/nemo_500_env/bin/mpif90
mpirun = /home/jhyun/.conda/envs/nemo_500_env/bin/mpirun
nc-config = /home/jhyun/.conda/envs/nemo_500_env/bin/nc-config
nf-config = /home/jhyun/.conda/envs/nemo_500_env/bin/nf-config
h5pcc = /home/jhyun/.conda/envs/nemo_500_env/bin/h5pcc

netCDF version : netCDF 4.9.2

netCDF prefix : /home/jhyun/.conda/envs/nemo_500_env

Parallel NetCDF : yes

## Slide 16
### Image OCR
[Image 1]
(nemo_500_env) SESE ETOCS cat arch/arch-conda.fcm

%CCOMPILER ${CONDA_PREFIX}/bin/mpicc

%FCOMPILER ${CONDA_PREFIX}/bin/mpif90

%LINKER ${CONDA_PREFIX}/bin/mpif90

%BASE_CFLAGS -w -03 -D BOOST_DISABLE_ASSERTS

%PROD_CFLAGS -DNDEBUG

%DEV_CFLAGS

%*DEBUG_CFLAGS -g

%BASE_FFLAGS -D__NONE__ -fdefault-real-8 -fdefault-double-8 -ffree-line-length-none

%PROD_FFLAGS -03 -fdefault-real-8 -fdefault-double-8 -ffree-Line-Length-none

%DEV_FFLAGS -g -fbacktrace -fdefault-real-8 -fdefault-double-8 -ffree-line-Length-none

%*DEBUG_FFLAGS -g -fbacktrace -fdefault-real-8 -fdefault-double-8 -ffree-Line-Length-none

%*BASE_INC -D__NONE__

%PROD_INC -D__NONE__

%*DEV_INC -D__NONE__

%*DEBUG_INC -D__NONE __

%BASE_LD -lstdc++ -lpthread

%PROD_LD -lstdc++ -lpthread

%DEV_LD -lstdc++ -lpthread

%DEBUG_LD -lstdc++ -lpthread

%CPP cpp

%FPP cpp -P

*MAKE make

(nemo_500_env) (RQqHSTGISOONSRITA/TenOSIOi0ss/Xi0SES cat arch/arch-conda.path

NCDIR="-I$CONDA_PREFIX/include"

NETCDF_LIBDIR="-L$CONDA_PREFIX/1ib"

NETCDF_LIB="-lnetcdff -lnetcdf"

MPI_INCDIR="$CONDA_PREFIX/include"

MPI_LIBDIR="$CONDA_PREFIX/1ib"

MPI_LIB="-1Lmpi"

HDF5_INCDIR="-I$CONDA_PREFIX/include"

HDF5_LIBDIR="-L$CONDA_PREFIX/1ib"

HDF5_LIB="-Lhdf5_hl -lhdf5"

OASIS_INCDIR="-I$0ASIS_HOME/build/build-static/lib/psmile.MPI1 \
-I$OASIS_HOME/build/build-static/lib/scrip \
-I$OASIS_HOME/build/build-static/lib/mct \
-I$OASIS_HOME/build/build-static/lib/mctdir"

OASIS_LIBDIR="-L$0ASIS_HOME/build/build-static/lib"

OASIS_LIB="-Lpsmile.MPI1 -lscrip -lmct -lmpeu"

## Slide 17
### Text objects
cd ${XIOS_HOME}
./make_xios --full --prod --arch conda --job 16

ls -al ${XIOS_HOME}/bin

NEMO5–XIOS3 실습 환경 설치

### Image OCR
[Image 1]
X ssh ©)
r_distribution_description.o server_from_client_source_filter.o server_to_client_store_filter.o eo 6:19:31 PM
service_node.o services.o services_manager.o sum_reduction.o symbols_core.o symbols_with_addr2line.o s¥me

ghelp.o symbols_with_dl.o symbols_with_libbacktrace.o symbols_with_libdwarf.o symbols_with_nothing.o temp

.0 temporal_splitting.o temporal_splitting_interface.o temporal_splitting_interface_attr.o temporal_trans

.0 ternary_arithmetic_filter.o thread_manager.o timer.o timer_interface.o timerremap.o token_manager.o tr
sfer_transform_connector.o transform_connector.o transform_filter.o transformation_path.o tree.o triple.o

o unary_arithmetic_filter.o unwind_with_execinfo.o unwind_with_nothing.o unwind_with_unwind.o unwind_with
ser_defined.o uuid.o variable.o variable_interface.o variable_interface_attr.o variablegroup_interface.o
up_interface_attr.o weight_transform_connector.o window_base.o window_manager.o workflow_graph.o xios.o x

ces.o xml_node.o xml_parser.o xml_parser_decl.o yacc_parser.o zoom_axis.o zoom_axis_interface.o zoom_axis

attr.o zoom_domain.o zoom_domain_interface.o zoom_domain_interface_attr.o

ar: creating /home/jhyun/nemo5.0-xios3/xios_3/tmp/lib__fcm__generic_testcase.a

ar: creating /home/jhyun/nemo5.@-xios3/xios_3/tmp/lib__fcm__xios_server.a

ar: creating /home/jhyun/nemo5.0-xios3/xios_3/tmp/libxios.a

/home/jhyun/.conda/envs/nemo_500_env/bin/mpif9@ -o xios_server.exe /home/jhyun/nemo5.@-xios3/xios_3/obj/se6:19:32 PM

o -L/home/jhyun/nemo5.@-xios3/xios_3/lib -l__fcm__xios_server -L/home/jhyun/.conda/envs/nemo_asmlys/lib -

netcdf -lhdf5_hl -lhdf5 -lmpi -lstdc++ -lstdc++ -lpthread

/home/jhyun/.conda/envs/nemo_500_env/bin/mpif9@ -o generic_testcase.exe /home/jhyun/nemo5.0-xios3/xios_3/

_testcase.o -L/home/jhyun/nemo5.0-xios3/xios_3/lib -1__fcm__generic_testcase -L/home/jhyun/.conda/envs/ne

ib -lnetcdff -lnetcdf -lhdf5_hl -lhdf5 -lmpi -lstdc++ -lstdc++ -lpthread

->Make: 163 seconds 6:19:36 PM
->TOTAL: 182 seconds

Build command finished on Mon Jul 27 18:19:35 2026. a

[Image 2]
(nemo_500_env) R@ynemoeS@0senv ~/nemo5.0-xi0s3/xi0s_3 > ls -al ${XIOS_HOME}/bin 6:22:09 PM
total 38532

drwxrwxr-x 2 jhyun jhyun 89 Jul 27 18:19 .

drwxrwxr-x 23 jhyun jhyun 4096 Jul 27 18:19 ..

lrwxrwxrwx 1 jhyun jhyun 43 Jul 27 18:19 (6) oy. <5) => /home/jhyun/nemo5.0-xios3/xios_3/fcm_env.s
-rwxrwxr-x 1 jhyun jhyun 20142200 Jul 27 18:19 generic_testcase.exe

-rwxrwxr-x 1 jhyun jhyun 19308072 Jul 27 18:19 xios_server.exe

## Slide 18
### Text objects
ldd $XIOS_HOME/bin/xios_server.exe | grep -E "mpi|netcdf|hdf5"

NEMO5–XIOS3 실습 환경 설치

### Image OCR
[Image 1]
(nemo_500_env) | nemo_50@_env ~ ldd $XIOS_HOME/bin/xios_server.exe | grep -E "mpi{netcdf{hdf5"
libnetcdff.so.7 => /home/jhyun/.conda/envs/nemo_500_env/lib/libnetcdff.so.7 (@x00002b0ee7f87000)
libnetcdf.so.19 => /home/jhyun/.conda/envs/nemo_500_env/lib/libnetcdf.so.19 (@x00002b0ee9063000)
libhdf5_hl.so.310 => /home/jhyun/.conda/envs/nemo_500_env/lib/lLibhdf5_hl.so.310 (@x@0002b0ee8061000)
libhdf5.so.310 => /home/jhyun/.conda/envs/nemo_500_env/lib/lLibhdf5.so.310 (@x@0002b0ee9296000)
libmpi.so.12 => /home/jhyun/.conda/envs/nemo_500_env/lib/libmpi.so.12 (@x00002b0ee96c0000)
libmpifort.so.12 => /home/jhyun/.conda/envs/nemo_500_env/lib/libmpifort.so.12 (0x00002b0eea959000)
libpnetcdf.so.6 => /home/jhyun/.conda/envs/nemo_500_env/lib/./libpnetcdf.so.6 (0x00002b0eeb6a3000)
Llibmpicxx.so.12 => /home/jhyun/.conda/envs/nemo_500_env/lib/././libmpicxx.so.12 (@x@0002b0eecdd3000)

## Slide 19
### Text objects
Directory layout

nemo_5.0.0/
├── src/       핵심 소스코드
├── cfgs/      reference / user configuration
├── arch/      compiler·library 설정
├── mk/        makenemo가 사용하는 build scripts
├── tools/     전처리·후처리 도구
├── sette/     표준 검증 테스트
└── makenemo   configuration 생성·컴파일

NEMO5

### Image OCR
[Image 1]
(nemo_500_env) REpeamenogsoolenrg ~/nemoS.O-xios3/nemo_5.0.0 Y main + —
total 84K

drwxrwxr-x 10 jhyun jhyun 4.0K Jul 27 13:54 arch
drwxrwxr-x 15 jhyun jhyun 4.0K Jul 27 13:54 cfgs
drwxrwxr-x 7 jhyun jhyun 94 Jul 27 13:54 ext
drwxrwxr-x 2 jhyun jhyun 4.0K Jul 27 13:54 griddes
-rw-rw-r-- 1 jhyun jhyun 21K Jul 27 13:54 LICENSE
drwxrwxr-x 2 jhyun jhyun 10 Jul 27 13:54 logs
-rwxrwxr-x 1 jhyun jhyun 24K Jul 27 13:54 makenemo
drwxrwxr-x 2 jhyun jhyun 4.0K Jul 27 13:54 mk
-rw-rw-r-- 1 jhyun jhyun 960 Jul 27 13:54 README.rst
drwxrwxr-x 2 jhyun jhyun 4.0K Jul 27 13:54 sct
drwxrwxr-x 4 jhyun jhyun 4.0K Jul 27 13:54 sette
drwxrwxr-x 11 jhyun jhyun 136 Jul 27 13:54 src
drwxrwxr-x 21 jhyun jhyun 4.0K Jul 27 13:54 tests
drwxrwxr-x 15 jhyun jhyun 4.0K Jul 27 13:54 tools

## Slide 20
### Text objects
ABL: Atmospheric Boundary Layer
ICE : sea ICE
NST: NeSTing tools
OCE : OCEan physics
OFF: OFFline
SAO: Stand-Alone Ocean
SAS: Simplified Atmospheric Surface
SWE: Shallow Water Equations
TOP: Tracers in the Ocean Paradigm

NEMO5

### Image OCR
[Image 1]
(1)

[Image 2]
Ce)

[Image 3]
(39

[Image 4]
(nemo_500_env) (R@QETONSOONERVI= 1s ${NEMO_HOME}/nemo_5.0.0/src
ABL ICE NST OCE OFF SAO SAS SWE TOP

## Slide 21
### Image OCR
[Image 1]
(nemo_500_env) ENEMIES) cat ${NEMO_HOME}/nemo_5.0.0/cfgs/ref_cfgs.txt
AGRIF_DEMO OCE ICE TOP NST

AMM12 OCE

C1D OCE ICE TOP

GYRE_BFM OCE TOP

GYRE_PISCES OCE TOP

ORCA2_OFF_PISCES OCE TOP OFF
ORCA2_OFF_TRC OCE TOP OFF
ORCA2_SAS_ICE OCE ICE NST SAS
ORCA2_ICE_PISCES OCE TOP ICE NST ABL
SPITZ12 OCE ICE

WED@25 OCE ICE

[Image 2]
(39

[Image 3]
(nemo_500_env) ¥inemegsONEENN > |s -al ${NEMO_HOME}/nemo_5.0.0/cfgs/ORCA2_ICE_PISCES
total 16

drwxrwxr-x 4 jhyun jhyun 84 Jul 28 11:59 .

drwxrwxr-x 16 jhyun jhyun 4096 Jul 28 11:59 ..

-rw-rw-r-- 1 jhyun jhyun 68 Jul 28 11:59 cpp_ORCA2_ICE_PISCES.fcm

drwxrwxr-x 2 jhyun jhyun 4096 Jul 28 11:59 EXPREF

drwxrwxr-x 2 jhyun jhyun 4096 Jul 28 11:59 IDL scripts

[Image 4]
(nemo_500_env) RQERGNSOONETTIIE cat ${NEMO_HOME}/nemo_5.0.0/cfgs/ORCA2_ICE_PISCES/cpp_ORCA2_ICE_PISCES. fcm
bld::tool::fppkeys key_si3 key_top key_xios key_qco key_vco_1d3d

## Slide 22
### Text objects
NEMO5 실습 – Q. OCE-ICE-OFF 실험은 어떻게 만들까?

echo " OCE_ICE_OFF OCE ICE" >> cfgs/ref_cfgs.txt

mkdir cfgs/OCE_ICE_OFF

echo "bld::tool::fppkeys key_si3 key_xios key_qco key_vco_1d3d" > cfgs/OCE_ICE_OFF/cpp_OCE_ICE_OFF.fcm

./makenemo -r OCE_ICE_OFF -n ORCA2_ICE_OFF -m conda-nemo5

(./makenemo -r OCE_ICE_OFF -m conda-nemo5)

## Slide 23
### Text objects
cd ${NEMO_HOME}/nemo_5.0.0/

./makenemo -r ORCA2_ICE_PISCES -n TEST_OCE_ICE_PISCES -m conda-nemo5 -j 16

cd ${NEMO_HOME}/nemo_5.0.0/cfgs/TEST_OCE_ICE_PISCES/EXP00

ln -sf ${XIOS_HOME}/bin/xios_server.exe ./

NEMO5 실습

## Slide 24
### Text objects
NEMO5 ./tests/VORTEX 실습

cd ${NEMO_HOME}/nemo_5.0.0/

./makenemo -a VORTEX -m conda-nemo5 -j 32

# 전에 에러 있었던 경우
#./makenemo -a VORTEX -m conda-nemo5 clean

cd ${NEMO_HOME}/nemo_5.0.0/tests/VORTEX/EXP00

ln -sf ${XIOS_HOME}/bin/xios_server.exe ./

mpirun -np 1 ./nemo : -np 1 ./xios_server.exe

### Image OCR
[Image 1]
“Ane rom we Gey 17
. [=|
}t 04
=|
t ise
-
>
ive
iv
19.1
. 19.0
‘
‘ . " : * a
‘ir

## Slide 25
### Text objects
NEMO5 ./tests/VORTEX 실습

Parent (root) grid

Child grid

VORTEX domain: 1800 km x 1800 Km x 5000 m

### Image OCR
[Image 1]
(nemo_500_env) 2, nemo_500_env ~ ~/nemo5.0-xios3/nemo_5.0.0/tests/VORTEX cat cpp_VORTEX.fcm
bld::tool::fppkeys key_xios key_agrif key_qco key_vco_1d

[Image 2]
&namusr_def ! User defined : VORTEX configuration: Flat bottom, beta-plane
nn

rn_dx = 30000. ! x horizontal resolution [meters]

rn_dy = 30000. ! y horizontal resolution [meters]

rn_dz = 500. ! z vertical resolution [meters]

rn_ppgphi0 = 38.5 ! Reference latitude [degrees]

rn_ppumax = 1.0

nn_rot = 0
/

[Image 3]
lessens”
&namdom ! time and space domain
(ee

rn_Dt = 1440. ! time step for the dynamics (and tracer if nn_acc=@)
/

[Image 4]
(nemo_500_env) [27 memo_500 env» ~/nemo5.0-xi0s3/nemo.5.0.0/tests/VORTEX/EXPOO > P main + cat AGRIF_FixedGrids.in
1

22 41 22 41 3 3 3

0
