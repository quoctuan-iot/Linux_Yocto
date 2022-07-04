# Summary - Linux Yocto Project

## 1.The build system

#### Poky

Poky has Bitbake, OE-Core (OpenEmbedded-Core) -> is maintained by the Yocto community.
    
    Poky -> Build the components

    - bootloader 
    - kernel
    - rootfs
    - Toolchains and SDKs( software development kits)

Poky only support (qemuarm): ARM, x86, x86-64, PowerPC, MIPS.

Poky contains three metadata dir.

    - meta: OE-Core metadata (ARm, x86,...), QEMU emulated hardware.
    - meta-yocto: Poky's distribution-specific 
    - meta-yocto-bsp: metadata for the ref hw-board
    
#### Bitbake

Bitbake as the make utilyty in Yocto -> parse the configuration, meta, schedule a task list and run through it.
-> is maintained by the Yocto and OE communities.

Poky, Bitbake -> is used by Yocto.

Yocto is able to run on Linux system:

    - git 1.7.8 ->
    - tar 1.24 ->
    - python 2.7 -> (not python3)

#### Yocto

Yocto project -> provides a ref build system (Poky)

BSPs(Board support package)

Ex: beaglebone, mpc8315e-rdb, genericx86, genericx86-64,..

To develop on different hardware -> need complement Poly with hardware-specific Yocto layers.

##### Creating a build dir

Before building first Yocto-image -> Need to create a build. (where the build output will be generated)

**One project** or **machine type** -> onlye one **build** dir.

Can all share a common **downloads**, **state cache**.

**HOW TO DO IT**

Using the oe-int-build-env script provided by Poky.

    source poky/oe-int-build-env build

**HOW IT WORKS**

build/conf:

    - bblayers.conf: this file lists the metadata layers to be considered for this project.
    - local.conf: project-specific configuration variables.
    - templateconf.cfg 

    TEMPLATECONF variable needs to refer to a directory templates for both local.conf and bblayer.conf

##### Building the first image

What type of image we want to build?

The different images: refer **Yocto Project Reference Manual**

What type of the machine?

Add machine to the conf/local.conf file.

---> Output: tmp/deploy/images/

##### Configuration network booting on Ubunt 16.04 to first boot.

**Installing a TFTP server**

    #Install tftpd-hpa
    sudo apt-get install tftpd-hp

    #Disable firewall
    sudo ufw disabl

    # Check status nfs-server
    service nfs-kernel-server status

**Installing an NFS server**

    #Create NFS dir
    sudo mkdir -p /nfs/

    #Install nfs-kernel-server
    sudo apt-get install nfs-kernel-server

    #Start a nfs-kernel-server
    sudo /etc/init.d/nfs-kernel-server start

    #Config NFS server
    echo "/nfs/ *(rw,no_subtree_check,sync,no_root_squash)" |sudo tee -a /etc/exports
    sudo exportfs -a

**I have a automation script to install TFTP and NFS on Ubuntu 16.04:** 

[Installing TFTP and NFS] (https://github.com/quoctuan-iot/Shell_scripts/blob/main/01_setup_env_ubuntu.sh)

When boot and stop at the U-boot prompt by pressing any key: Some command to write environment boot.

Configure static IP: 

    setenv ipaddr <static_ip>

Configure IP on server host.

    setenv serverip <host_ip>

Configure the rootfs, image, device tree

    setenv nfsroot /nfs

Save environment

    saveenv

##### Sharing downloads

To optimize the build times by sharing download.

Setting up **DL_DIR** in conf/local.conf file.

    DL_DIR ?= "${BSPDIR}/downloads/"

##### Sharing state cache

Yocto builds everything from source. A new project -> Only the configuration is created.

If the input data changes, the task need to be rebuilt.

Setting up **SSTATE_DIR** in conf/local.conf file.

    SSTATE_DIR ?="state-dir"


## 2. The BSP Layer

