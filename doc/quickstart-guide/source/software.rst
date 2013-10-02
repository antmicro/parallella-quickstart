Software build and run guide
============================

This section provides information on acquiring, configuring, building and running software on Parallella board. 

Prerequisites
-------------

This section lists software and tools required in the next steps of building and running Linux on Parallella board. In the following subsections is is assumed that every requisite described in this part is met.
Furthermore, for every required package, place from where it can be obtained is provided.

.. rubric:: Xilinx Tools

Tools provided by Xilinx required by some parts of this section are as follows:

#. Xilinx Software Development Kit (SDK) is required for generating and building First Stage Boot Loader (FSBL). If it is not required for your project to change hardware configuration, thus replacement of FSBL you do not need this tool. 

#. Xilinx Microprocessor Debugger (XMD) is required to connect with Zynq chip via JTAG. If you do not plan do load or debug software this way you do not need this tool.

#. Bootgen is required for preparing bootable image for Zynq chip.

TODO: WRITE ABOUT OPENOCD!!!!

If any of above mentioned cases applies to your project you need to download and install Xilinx ISE Design Suite from http://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/design-tools.html. As installation type select "Embedded edition". Note that this software isn't free and needs extra licensing.  

.. rubric::  ARM toolchain  

Xilinx tools comes with 'arm-xilinx-eabi-' and 'arm-xilinx-linux-' toolchains. Both of them can be used in next steps. If you do not need or don't want to use Xilinx tools any other Arm toolchain (e.g. CodeSourcery lite edition available here: http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/) can also be used in the next steps. 

.. rubric:: U-Boot tools

U-Boot tools are required for generation loadable image of Linux kernel. 

On Debian/Ubuntu distributions this software can be obtained with: 

.. code-block:: bash 

   apt-get install u-boot-tools

TODO: OTHER DISTROS

.. _source:

Getting the source
------------------

This section discuss obtaining the source code of bootloaders and Linux kernel.
If you do not want to build your own version of mentioned software you can skip this part and go directly to :ref:`binaries`.

.. rubric:: FSBL

First stage boot loader code is generated from Xilinx Software Development Kit.
More info about creating and building it can be found (TODO: reference to Xilinx guide [if any])

.. rubric:: U-Boot
   
Official U-Boot source code for Parallella board can be found at: https://github.com/parallella/parallella-uboot

.. code-block:: bash 

   #get source 
   git clone https://github.com/parallella/parallella-uboot
   #checkout correct branch 
   git checkout parallella-gen0

.. rubric:: Linux 

Official Linux source code for Parallella board can be found at: https://github.com/antmicro/linux-parallella

.. code-block:: bash 

   #get source 
   git clone https://github.com/antmicro/linux-parallella
   #checkout correct branch
   git checkout parallella-linux3.9        

.. _build:

Software Building
-----------------

This section discuss building bootloaders and Linux kernel from source obtained previously. If you do not want to build your own version of mentioned software you can skip this part and go directly to :ref:`binaries`.

.. rubric:: First Stage Bootloader (FSBL)

TODO: Reference official Xilinx guide 

.. rubric:: Build environment setup

Before attempting to build U-Boot and Linux kernel image you must configure build environment. This can be done with following procedure 

.. code-block:: bash

   #set architecture type 
   export ARCH=arm
   #set cross compiler prefix
   export CROSS_COMPILE=<your_toolchain_prefix> #e.g. arm-xilinx-eabi-
   #set PATH
   export PATH=/path/to/your/toolchain:$PATH

.. rubric:: U-Boot 

U-Boot build procedure:

.. code-block:: bash 

   #configure build 
   make parallella_config 
   #build U-Boot 
   make [-jX] 

.. rubric:: Linux

Linux build procedure:

.. code-block:: bash 

   #configure build 
   make parallella_defconfig
   #build kernel image 
   make [-jX] uImage

.. _bootgen:

Preparing Zynq boot image
-------------------------

This section describes preparation of Zynq boot image that can be write into onboard flash memory of Parallella board. If you do not plan to change bootloader you can skip this section. Bootgen tool requires .bif input file describing boot image layout. An example of bif file is listed below:

.. code-block:: bif

   the_ROM_image:
        {
        [bootloader]/path/to/fsbl.elf
        /path/to/u-boot.elf
        }

With this bif configuration simple boot image containing only first and second stage bootloader (FSBL and U-Boot) can be build with below command:

.. code-block:: bash

   bootgen -image /path/to/image.bif -o i parallella.bin

Result file ("parallella.bin") is bootable image that can be program into Parallella onboard flash memory. More information about using bootgen and creating Zynq boot images can be found in `Zynq-7000 All Programmable SoC Software Developers Guide <http://www.xilinx.com/support/documentation/user_guides/ug821-zynq-7000-swdev.pdf>`_.

.. _binaries:

Precompiled binaries
--------------------

TODO: Reference binaries repository

Repository file list:
* fsbl.elf - compiled First Stage Boot Loader
* u-boot.elf - compiled U-Boot 
* uImage - Linux kernel image (u-boot loadable format)
* devicetree.dtb - compiled device tree for the Parallella board 
* parallella.bit - Zynq bitstream 
* parallella.bit.bin - Zynq bitstream (u-boot loadable format)

Bootloader deployment
---------------------

This section describes deployment procedure of previously compiled or downloaded bootloaders binaries. There are two ways of getting run new bootloader software on Parallella board:

#. Program flash with new Zynq boot image file see :ref:`flashing`.
#. Download and run bootloader via JTAG connection see :ref:`jtag`.

.. note:: If you plan to re flash device you need to have proper Zynq boot image see :ref:`bootgen` or :ref:`binaries`

.. _jtag:

JTAG
++++

.. note:: JTAG deploy requires use of Xilinx Platform Cable to connect to the Parallella board. 

#. Run xmd

   .. code-block:: tcl

      connect arm hw
      fpga -f /path/to/your/bitstream
      source /path/to/your/ps7_init.tcl
      ps7_init
      init_user
      source stub.tcl
      target 64

#. Continue in xmd 

   .. code-block:: tcl

      dow </path/to/yours/>u-boot.elf
      con
     
#. or Run gdb 

   .. code-block:: gdb

      target remote localhost:1234
      file </path/to/yours/>u-boot.elf
      load
      c

.. _flashing:
   
Program Flash
+++++++++++++ 

.. warning:: Reprogramming flash with incompatible Zynq boot image may result in inability to use the Parallella board. Moreover, during flashing, stable power supply must be assured. 

.. note:: If board was programmed with wrong Boot image or there was other problem during flashing it still can be bring up using JTAG procedure (see :ref:`jtag`).

U-Boot delivered with the Parallella board can be used for re-flashing the board. Binary available from repository (:ref:`binaries`) or build (:ref:`build`) from github source (:ref:`source`) has also this functionality. If you changed default bootloader and it do not provide this feature you can still run default official Parallella U-Boot via JTAG (:ref:`jtag`) or by loading it with your current bootloader. 

To re-flash the Parallella board using official U-Boot follow these steps:

#. Remove SD card from the slot.
#. Power up the board - U-Boot should start, and lack of SD card will prevent it from booting linux.
#. Put SD card into the slot.
#. Run following commands in U-Boot prompt.

   * Initialize mmc subsystem 

     .. code-block::u-boot

        mmc info 

   * Load Zynq boot image from first partition (FAT formatted) into RAM memory (make sure the image file is present on the SD card)
     
     .. code-block:: u-boot

        fatload mmc 0 <boot_image_name> 0x4000000 

   * Initialize spi flash subsystem 

     .. code-block:: u-boot

        sf probe 0 0 0

   * Erase whole flash memory 

     .. code-block:: u-boot

        sf erase 0 0x1000000 
   
   * Program the flash memory 

     .. code-block:: u-boot

        sf write 0x4000000 0 <boot_image_length> 

#. Power cycle the board.

.. note:: Boot image length is displayed when file is loaded into RAM memory from SD card.


.. _booting:

Booting Linux
=============

This section discuss few (of many possible) ways of booting Linux on the Parallella board. If you are interested in general boot procedure of Zynq based device refer to `Zynq-7000 All Pr    ogrammable SoC Software Developers Guide <http://www.xilinx.com/support/documentation/user_guides/ug821-zynq-7000-swdev.pdf>`_.

Booting from SD card / USB drive
--------------------------------

Copy uImage, parallella.bit.bin and devicetree.dtb files onto first partition of SD card (FAT formatted), insert card into the board and power up it. Unpack Linux root file system onto USB drive or second partition of SD card (ext formatted).

.. note:: Remember of setting proper boot device address in bootargs (/dev/sdaX for USB drive boot or /dev/mmcblk0pX for SD card) 
