A few caveats:

1. The confg files are written in python2, however, most modern systems utilize python3. To fix syntax errors, do the following:
   a. cd /usr/bin/
   b. sudo rm -r python
   c. sudo ln -s python2.7 python

   Note: Be sure to revert this change after the building process is complete, to avoid breaking your system.

   d. sudo rm -r python
   e. sudo ln -s python2.7 python

2. The drivers "drivers/input/touchscreen/mediatek/GT1151/gt1x_tpd.c" and "drivers/input/touchscreen/mediatek/GT5663/gt1x_tpd.c" have been modified to allow the touchscreen to work in recovery mode.
   If you wish to revert these changes, replace them with their original drivers present in "main" branch. And the drivers modified for recovery touchscreen is in the "touchscreen_drivers untouched", apologies for the confusion.

Build Instructions:

1. Kernel configuration files
   arch/arm/configs:
   Nokia 1 (TA-1047/TA-1060/TA-1056/TA-1079/TA-1066): FRT_defconfig

2. How to build

  Prerequisites:

    * ramdisk.img - root fs

    * mkbootimg - boot.img generator

    * The ARM cross-compiler - Included within the source

  Step 1: Build Kernel (zImage)
    # You must be at the root of the kernel source, before running these commands
    
    $ export ARCH=arm

    $ export CROSS_COMPILE=<root_directory_of_source>/gcc-arm-none-eabi-10.3-2021.10/bin/arm-none-eabi-

    $ mkdir out/
    
    $ make FRT_defconfig menuconfig O=out/
    
      NOTE: Select the relavant options in the menuconfig window, after  doing so, exit out of it.

    $ make O=out/ -j16

    Note: Increasing the number of jobs could result in significantly lower build times. Consider CCACHE, or adding a ~15GB SWAP file/partition to avoid OOM errors, during soong initialization.
    Yor built kernel will be at arch/arm/boot/ if you succeed in building the kernel.

  Step 2: Assembling the boot.img
    (In the output directory)
	$ mkbootimg --kernel arch/arm/boot/zImage-dtb --cmdline "bootopt=64S3,32N2,32N2 buildvariant=userdebug" \
	  --base 0x40000000 --kernel_offset 0x00008000 --ramdisk_offset 0x04000000 --tags_offset 0xE000000 \
	  --ramdisk ramdisk.img --output boot.img

A massive thanks and credit to:
https://github.com/iykex - for giving the base building instructions.
https://github.com/forforksake/android_kernel_samsung_a145p/commit/ecdcea206bbc7d11c776cba03fa7420bca32768b?diff=split - and to @forforksake for giving general pointers to fixing the touchscreen in recovery mode.
