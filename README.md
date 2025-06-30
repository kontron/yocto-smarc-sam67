# SMARC-sAM67 Yocto BSP


## Description
This repository contains configuration files and helper scripts to build the Kontron SMARC-sAM67 Yocto BSP from scratch on an appropriate build machine. It is based on the TI processor SDK and uses the oe-layertool-setup.sh helper script for downloading the necessary repos and preparation of the build environment as well as configuration files describing the necessary repositories with appropriate source revisions required for building.
A new configuration file (sdk-linux-sa67.txt) has been created for the SMARC-sAM67 device.

## Prerequisites
Building this Yocto BSP has been tested on Ubuntu 20.04.6 LTS build host. It should also work on similar linux distributions like Debian 12.x or newer or on more recent Ubuntu versions. Make sure that sufficient disk space (>150 GB for the smallest build targets) is available.

## Building

To prepare the SA67 Yocto BSP for building, follow the steps below.

```
cd yocto-smarc-sam67
./oe-layertool-setup.sh -f configs/sdk-linux-sa67.txt
cd build
. conf/setenv
```

After sourcing the conf/setenv, the bitbake environment is available and a bunch of build targets from different providers is shown. For the SA67 the tisdk based targets are used as there are based on the Arago TI SDK which is also used for TI's J722S EVM platform the SA67 has been derived from.

- tisdk-tiny-image

This is the Arago TI SDK super minimal base image.

- tisdk-base-image

This is the Arago TI SDK base image with test tools.

- tisdk-default-image

This is the Arago TI SDK full filesystem image with weston/wayland support. Note that building this image will take approx. 750 GB of disk space.

E.g. to build the tisdk-tiny-image for the sa67-mainline machine, use the following command.

```
MACHINE=sa67-mainline bitbake -k tisdk-tiny-image
```

## Building using Siemens KAS

In the kas folder, configuration files have been added to use Siemens KAS for building the Yocto BSP. It is assumed that kas version 4.6 or later has been installed on the build machine. See the KAS user guide at https://kas.readthedocs.io/en/latest/userguide.html for more detail.

Before building the SMARC sAM67 Yocto BSP with kas, bitbake must be installed in the kas working directory first. It is not possible to configure the bitbake repo url in the sa67-sdk-scarthgap-config.yml config file along with the yocto layer repos. Thus the build steps using kas would be as follows.

```
cd yocto-smarc-sam67
cd kas
git clone https://git.openembedded.org/bitbake --branch "2.8"
kas build --target tisdk-tiny-image configs/sa67-sdk-scarthgap-config.yml
```

In case that a different build directory is desired to separate the build folder from the kas working directory containing the source repos, the KAS_BUILD_DIR which is the same as the KAS_WORK_DIR by default can be changed. It is also possible to override the sa67-mainline machine from the .yml file by setting the KAS_MACHINE environment var.

```
KAS_MACHINE=sa67 KAS_BUILD_DIR=../kas_build kas build --target tisdk-tiny-image configs/sa67-sdk-scarthgap-config.yml
```

### Handling Credentials with KAS

KAS supports credential handling in case that this is required when reading the different layer repositories. Among others, the .netrc file which is normally located in the users home directory.

Example .netrc file:

```
machine gitlab.kontron.com
login <username>
password <password>
```

To use kas with the netrc user credentials, the netrc file is passed with the NETRC_FILE environment variable.

```
NETRC_FILE=/path/to/.netrc KAS_BUILD_DIR=../kas_build kas checkout configs/sa67-sdk-scarthgap-config.yml
```

## Installation

When build has been finished, the build artefacts can be found in the deploy_ti/images/<machine>/ folder. Among the artefacts are bootfiles (u-boot, tiboot3, bl31), linux kernel image and a (packed) rootfs wic image that can be installed directly either on SD card or on onboard emmc. E.g. to install the tisdk-tiny-image wic image on SD card for booting, attach the SD card to your host machine and use the following command.

```
dd if=tisdk-default-image-sa67-mainline.rootfs.wic of=/dev/sdX bs=1M conv=notrunc,sync status=progress
```

where sdX is the device file of the SD card when attached to the host computer.


## Support


## Contributing


## Authors and acknowledgment


## License


## Project status

active, under development
