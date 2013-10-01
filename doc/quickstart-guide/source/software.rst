Software build and run guide
============================

This section provides information on acquiring, configuring, building and running software on Prallella board. 

Prerequisites
-------------

This section lists software and tools required in the next steps of building and running Linux on Parallella board. In the following subsections is is assumed that every requisite described in this part is met. Furthermore, for every required package, place from where it can be obtainded is provided.

.. rubric:: Xilinx Tools

Tools provided by Xilinx required by some parts of this section are as follows:

#. Xilinx Software Developement Kit (SDK) is required for generating and building First Stage Boot Loader (FSBL). If it is not required for your project to change hardware configuration, thus replacement of FSBL tou do not need this tool. 

#. Xilinx Microprocessor Debbuger (XMD) is required to connect with Zynq chip via JTAG. If you do not plan do load or debug software this way you do not need this tool.

TODO: WRITE ABOUT OPENOCD!!!!

If any of above mentioned cases applies to your project you need to download and install Xilinx ISE Design Suite from http://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/design-tools.html. As instalation type select "Embedded edition". Note that this software isn't free and needs extra licensing.  

.. rubric::  ARM toolchain  

Xilinx tools comes with 'arm-xilinx-eabi-' and 'arm-xilinx-linux-' toolchains. Both of them can be used in next steps. If youn do not need or don't want to use Xilinx tools any other Arm toolchain (e.g. CodeSourcery edition availiabe here: http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/) can also be used in the next steps. 

.. rubric:: U-Boot tools

U-Boot tools are required for generation loadable image of Linux kernel. 

On Debian/Ubuntu distributions this software can be obtained with: 

.. code-block:: bash 

   apt-get install u-boot-tools

TODO: OTHER DISTROS

Getting the source
------------------

.. rubric:: FSBL

First stage boot loader code is generated from Xilinx Software Developemnt Kit. More info about creating and 

.. rubric:: U-Boot
   
Official U-Boot source code for parallella board can be found in: https://github.com/parallella/parallella-uboot

.. code-block:: bash 

   #get source 
   git clone https://github.com/parallella/parallella-uboot
   #checkout correct branch 
   git checkout parallella-gen0

.. rubric:: Linux 

Official Linux source code for parallella board can be found in: https://github.com/antmicro/linux-parallella

.. code-block:: bash 

   #get source 
   git clone https://github.com/antmicro/linux-parallella
   #checkuot correct branch
   git checkout parallella-linux3.9        

Building
--------

.. rubric:: First Stage Bootloader (FSBL)

Reference official Xilinx guide 

.. rubric:: Build environent setup

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

Precompiled binaries
--------------------

Deployment
----------

JTAG
++++

#. Run xmd

   .. code-block:: tcl

      connect arm hw
      fpga -f /path/to/your/bitstream
      source /path/to/your/ps7_init.tcl
      ps7_init
      init_user
      source stub.tcl
      targer 64

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

   
Program Flash
+++++++++++++ 

Reference to or rewrite parallella guide ...

Booting Linux
=============

Put it all on SD card and run ...
