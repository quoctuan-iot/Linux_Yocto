# Raspberry pi4 (Boot RPi using u-boot and initramfs)

**PinOut Raspberry-pi4 (4Gb)**

![image](/Raspbery_pi4/images/Raspberry-Pi-4-Pinout.png)

**Connecting CP2102 USB to Serial Converter to Raspberry Pi**

![image](/Raspbery_pi4/images/Connect_cp2102_rasp.png)

**Ubuntu 16.04**

## Creating a Cross-Platform Toolchain for Raspberry Pi 4

#### Install the dependencies

    sudo apt-get update
    sudo apt-get install automake bison chrpath flex g++ git gperf gawk help2man libexpat1-dev libncurses5-dev libsdl1.2-dev libtool libtool-bin libtool-doc python2.7-dev texinfo

#### Get the source code

    git clone https://github.com/crosstool-ng/crosstool-ng.git
    cd crosstool-ng/
    
#### Build and install

    ./bootstrap
    ./configure --enable-local
    ./configure --prefix=${PWD}
    make
    make install

##### **Creating the toolchain for RPi4**
Selecting a base-line **RPi_3**

    ./ct-ng aarch64-rpi3-linux-gnu

If there are **RPi-4** in samples. Don't need to customization only **select** an **build**

    ./ct-ng aarch64-rpi4-linux-gnu

##### Customization

    ./ct-ng menuconfig

![image](/Raspbery_pi4/images/menuconfig.png)

1. Paths and misc options -> Render the toolchain read-only ->false

2. Target options -> Emit assembly for CPU: Change cortex-a53to cortex-a72

3. Toolchain options -> Tuple’s vendor string: Change rpi3 to rpi4

##### Build
    
    ./ct-ng build

##### Error: 

**Retrieving 'isl-0.20' download failed.**

cd .build/tarballs

wget https://libisl.sourceforge.io/isl-0.20.tar.gz

wget https://github.com/libexpat/libexpat/releases/tag/R_2_2_6/expat-2.2.6.tar.bz2

re-build.

(Git source](https://github.com/crosstool-ng/crosstool-ng/tree/master/samples)

## Bootloader

Bootloader is specific device. 

#### Download u-boot

    git clone git://git.denx.de/u-boot.git
    cd u-boot
    git checkout v2021.10 -b v2021.10

#### Configure (Using toolchain environment that is generated in above step)

    export PATH=${HOME}/x-tools/aarch64-rpi4-linux-gnu/bin/:$PATH
    export CROSS_COMPILE=aarch64-rpi4-linux-gnu-
    make rpi_4_defconfig

#### Build 

    make

#### Install U-boot to SD-card

##### Setting up SD-card

The SD-card will be used to store the bootloader, the rootfs. 
- First partition: *boot* (FAT32 format) -> store bootloader
- Second partition: *root* (ext4 format) -> store rootfs

###### Find the SD card name

Inserting the SD card reader to the Linux desktop machine, or any board

    lsblk

###### Delete existing partitions

    sudo fdisk /dev/sdb
    Command (m for help)

###### Add two partitions

    sudo fdisk /dev/sdb
    Command (m for help)

*boot*: Should be > 100Mb

*root*: of the remaining size

###### Format the partitions

FAT32 for boot partition

    mkfs.vfat -F 32 -n boot /dev/sdb1

ext4 for root partition

    mkfs.ext4 -L root /dev/sdb2

###### Mount the partitions

To copy data to SD-card, we need to mount a partition.

    mount /dev/sdb1 /mnt/boot
    mount /dev/sdb2 /mnt/root

After done. We need to **umount**

    umount /mnt/boot
    umount /mnt/root
___

# Yocto Project for Raspberry Pi4 
