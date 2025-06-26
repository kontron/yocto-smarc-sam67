# SMARC-sAM67 Yocto BSP


## Description
This repository contains configuration files and helper scripts to build the Kontron SMARC-sAM67 Yocto BSP from scratch on an appropriate build machine. It is based on the TI processor SDK and uses the oe-layertool-setup.sh helper script for downloading the necessary repos and preparation of the build environment as well as configuration files describing the necessary repositories with appropriate source revisions required for building.
A new configuration file (sdk-linux-sa67.txt) has been created for the SMARC-sAM67 device.

## Prerequisites
Building this Yocto BSP has been tested on Ubuntu 20.04.6 LTS build host. It should also work on similar linux distributions like Debian 12.x or newer or on more recent Ubuntu versions. Make sure that sufficient disk space (>150 GB for the smallest build targets) is available.

## Building

To prepare the SA67 Yocto BSP for building, follow the steps below.

```
cd kontron-sa67-yocto
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
