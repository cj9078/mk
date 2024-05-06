# D2H7790_IMG24_Software
This repository contains the IMG24 software.

Repository is created from [Magna-Electronics/L2H5008_Sabine_SA80NFE20T](https://elc-github.magna.global/Magna-Electronics/L2H5008_Sabine_SA80NFE20T), and striped down from old deleted, unncecery files.
All history is kept. Very old commits will not build.

# Build_Software_User_Guide

This user guide serves to provide a guideline to all the software engineers how to setup build tools and build software.

If not otherwise sated, all relative paths used here are relative from git repository root folder.

#### Table of Contents

1. [Build tools](#1-build-tools)
    - [Install tools](#11-install-tools)
    - [Setup Conan](#12-setup-conon)
    - [Setup necessary licenses](#13-setup-necessary-licenses)
2. [Build Magna software](#2-build-magna-software)
    - [CMake presets](#21-cmake-presets)
    - [Build from command line](#build-from-command-line)
        - [Other useful commands1](#221-other-useful-commands)
3. [Build Under software](#3-build-under-software)
    - [Setup environment](#31-setup-environment)
    - [Build Uhnder SRS (Magna patched SRS)](#32-build-uhnder-srs-magna-patched-srs)
4. [QAC (Magna software)](#4-qac-magna-software)
    - [Generate QAC projectbn](#41-generate-qac-project)
        - [Generate QAC project from command line](#411-generate-qac-project-from-command-line)
5. [Appendix](#5-appendix)
    - [Git setup](#51-git-setup)
        - [Add git to path (Windows only)](#511-add-git-to-path-windows-only)
        - [Git config](#512-git-config)
    - [Get and change modulecfg](#52-get-and-change-modulecfg)
        - [Setup virtual environment](#521-setup-virtual-environment)
        - [Build Uhnder](#522-build-uhnder)
        - [Get (and edit) module config file](#523-get-and-edit-module-config-file)


---



## 1. Build tools

To build Magna SW following tools are needed:


* Ninja - small build system with a focus on speed
* CMake - cross-platform free and open-source software for build automation, testing, packaging and installation of software by using a compiler-independent method
* Green Hills ARM compiler
* Tensilica's Xtensa C/C++ compiler
* Python3 - interpreted high-level general-purpose programming language
* Helix QAC - static code analysis tools for C/C++

To build Uhnder SW following tools are needed:

* TODO

### 1.1 Install tools

“packager” is used to install tools. Please check https://magnalm-prd.ptcmscloud.com:7001/si/viewrevision?projectName=%23/L2H5008%23KP03_ProductEngineering/40_Software/20_Construction/Documentation&selection=Software_Configuration_and_Integration_Handbook.docx Chapter “3.3 Tool repositories – Packager”, how to install packager.

Build tools are available in Magna Artifactory. To install tools use packager:
```
C:\prjtools\packager.exe -c Config\config_package_dev.json install
```

Add paths to %PATH% environment variable. Run “Edit environment variables for your account”, and add flowing paths to %PATH%:
```
C:\prjtools\ninja\ver_1.10.1
C:\prjtools\cmake\ver_3.20.0\bin
C:\prjtools\python\ver_3.7.6_p3
C:\prjtools\python\ver_3.7.6_p3\Scripts
C:\prjtools\srecord\ver_1.63
```
To finalize Helix QAC installation follow instructions inside:
```
C:\prjtools\prqa\ver_2020.2\README.txt
```

> **Note**: Tool versions are according to version used during document creation. Check installed versions inside Config\config_package_dev.json file.

### 1.2 Setup Conan

Unlock your user profile: https://elc-jfrog.magna.global/ui/admin/artifactory/user_profile and copy “Encrypted password”:

<img src="readme_images/JFROG_1.png" alt="drawing" width="85%"/>


Add your login credentials variables to your environment variables (run “Edit environment variables for your account”):

```
ME_ARTIFACTORY_PWD=your encrypted password
ME_ARTIFACTORY_USER=your username
```
Finalize setup by running:
```
Tools\Conan_Profiles\conan_setup.bat
```
### 1.3 Setup necessary licenses

For Tensilica (dsp) compiler add following variable to your environment variables:
```
XTENSAD_LICENSE_FILE=59835@MEESAILLIC07.magna.global
```
For arm compiler add following variable to your environment variables:
```
GHS_LMHOST=@MEEMSDCLIC09.magna.global:2010,MEEMSDCLIC09.magna.global:2009
```

## 2. Build Magna software

Every Project we build has its root folder with following files:

- CMakeLists.txt   
- CMakePresets.json

Project root folders:

- ACP\Application
- ACP\Authenticator
- ACP\BootLoader
- CCP\Application
- DSP\Application
- HSM\Application
- HSM\AppLoader
- HSM\BootLoader
- HSM\BootMgr

Building with CMake is always 2 steps process:
1. Configure Project – need to be done only once
2. Build Project

### 2.1 CMake presets


We use CMake presets. Instructions here are written to build Development “dev” configuration/preset.

To check available presets run (from Project root folder):
```
cmake --list-presets all
```

In vscode available presets can be selected from drop down menus.

### Build from command line

In command prompt go to Project root folder, example:

```
cd ACP\Application
```
	
Configure Project:

```
cmake --preset dev
```

Build Project:

```
cmake --build --preset dev
```

#### 2.2.1 Other useful commands

Clean:
```
cmake --build --preset dev --target clean
```

Clean rebuild:
```
cmake --build --preset dev --clean-rebuild
```

> **Note**: Clean and Clean rebuild, are not necessary and usage shall be avoided. If case is found where incremental build does not produce same resalts as clean rebuild, please report it to the Author of this document.

Clean configuration (needed after CMake toolchain changes in folder Tools\cmake_buildsys\cmakemodules):
```
delete Build folder
```


## 3. Build Under software

### 3.1 Setup environment

Under is bult under Linux OS. IT provided Linux virtual machine “meelxdcradar01.magna.global” and shared drive “\\meelxdcquantum\L2H5008".

Add following to your .profile file for Arm compiler:
```
export ARMLMD_LICENSE_FILE=54897@meemsdclic10.magna.global
export ARM_PRODUCT_DEF=/opt/prjtools/arm-compiler/ver_6.16.1/sw/mappings/gold.elmap
export PATH=/opt/prjtools/cmake/cmake-3.22.3-linux-x86_64/bin:/opt/prjtools/arm-compiler/ver_6.16.1/bin:$PATH
```

Add following to your .profile file for Tensilica compiler (for unpatched SRS build – master branch):
```
export XTENSAD_LICENSE_FILE=59835@MEESAILLIC07.magna.global
export XTENSA_CORE=p5_Uhnder_2BTCM_64I_5
export XTENSA_SYSTEM=/opt/prjtools/tensilica/ver_2021.6.0/builds/RI-2021.6-linux/p5_Uhnder_2BTCM_64I_5/config
export PATH=/opt/prjtools/tensilica/ver_2021.6.0/tools/RI-2021.6-linux/XtensaTools/bin:$PATH
```

### 3.2 Build Uhnder SRS (Magna patched SRS)

From Repository root run:
```
./Tools/Uhnder/build.sh
```
## 4. QAC (Magna software)

### 4.1 Generate QAC project

QAC project file is not checked in git. We use CMake to generate it. After generation QAC project file is generated in (from Project root):
```
Test\QAC\prqaproject.xml
```
Once project is generated open it with Helix QAC tool.

#### 4.1.1 Generate QAC project from command line

Assumed that Project is configured, run:

```
cmake --build --preset dev --target qaf
```

## 5. Appendix

### 5.1 Git setup

#### 5.1.1 Add git to path (Windows only)

Add git to %PATH% environment variable in following order (you will have same features from standard command prompt as in GitBash).

```
C:\prjtools\git\ver_???\cmd
C:\prjtools\git\ver_???\bin
C:\prjtools\git\ver_???\usr\bin
```



#### 5.1.2 Git config

> **Note**: For git config in linux use single quotes instead of double quotes

Git initial configuration:
```
git config --global user.name "LAST_NAME, FIRST_NAME"
git config --global user.email your.email@magna.com
git config --global user.username FIRST_NAME
git config --global core.autocrlf false
git config --global init.defaultBranch develop
```
Git submodule update alias (run with ‘git sup’):

```
git config --global alias.sup "!git submodule sync && git submodule update --init --recursive"
```

Git clean all alias (run with ‘git clean-all’):
```
git config --global alias.clean-all "!git clean -xffd -e *.elf && git submodule foreach --recursive git clean -xffd"
```
Installing Git Large File Storage: 
```
git lfs install
```

### 5.2 Get and change modulecfg

Example is executed from Uhnder_Sabine repository root folder.

> **Note**: Description was done with Under SRS_8.1.0_U1, wheel (whl) files version could be different.

#### 5.2.1 Setup virtual environment

```
python3 -m venv ~/uhenv
source ~/uhenv/bin/activate
pip install --upgrade pip
pip install SRS/bin/regutil/regutil-0.6.0-py3-none-any.whl
pip install SRS/src/radar-remote-api-sdk/python/wheels/ubuntu_18/pyrra-0.81.1-cp36-cp36m-manylinux1_x86_64.whl
deactivate
```

#### 5.2.2 Build Uhnder

```
cd SRS/src/system-radar-software/build/
python3 configure.py --board cervelo-mrr --sim --split --build
```

#### 5.2.3 Get (and edit) module config file

```
cd cervelo-mrr-split-sim
source ~/uhenv/bin/activate
tftp get modulecfg.bin modulecfg
modulecfg --filename modulecfg.bin
```
