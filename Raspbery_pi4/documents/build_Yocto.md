# Yocto Project for Raspberry Pi4

## Build Yocto

##### Installing Yocto dependencies on Ubuntu.

    sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev xterm

    sudo apt install git git-lfs tar python3 python3-pip gcc


##### Download source code 

    git clone git://git.yoctoproject.org/poky -b dunfell

    git clone git://git.openembedded.org/meta-openembedded -b dunfell

    git clone git://git.yoctoproject.org/meta-raspberrypi -b dunfell

##### Configuration bblayers

Setting up the build environment

    source oe-init-build-env

##### Add meta to bblayer

At Yocto directory

    bitbake-layers add-layer ../meta-raspberrypi
    bitbake-layers add-layer ../meta-openembedded/meta-oe
    bitbake-layers add-layer ../meta-openembedded/meta-python
    bitbake-layers add-layer ../meta-openembedded/meta-networking
    bitbake-layers add-layer ../meta-openembedded/meta-multimedia

Check layers are added.

    bitbake-layers show-layers

##### Set the machine variable

*Edit conf/local.conf file*

MACHINE ??= "raspberrypi4"

**Ex: Enable UART**

ENABLE_UART = "1"