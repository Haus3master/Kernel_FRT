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

    $ export CROSS_COMPILE=/media/hausemaster/Nokia1_k/LINUX/android/kernel-4.9-lc/gcc-arm-none-eabi-10.3-2021.10/bin/arm-none-eabi-

    $ mkdir out/
    
    $ make FRT_defconfig menuconfig O=out/
    
      NOTE: Select the relavant options in the menuconfig window, after  doing so, exit out of it.

    $ make O=out/

    Yor built kernel will be at arch/arm/boot/ if you succeed in building the kernel.

  Step 2: Assembling the boot.img
    (In the output directory)
	$ mkbootimg --kernel arch/arm/boot/zImage-dtb --cmdline "bootopt=64S3,32N2,32N2 buildvariant=userdebug" \
	  --base 0x40000000 --kernel_offset 0x00008000 --ramdisk_offset 0x04000000 --tags_offset 0xE000000 \
	  --ramdisk ramdisk.img --output boot.img
