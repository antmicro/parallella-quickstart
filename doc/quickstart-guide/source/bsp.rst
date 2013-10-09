Building the Linux BSP
======================

This section describes how to download, configure and build the Linux BSP for the Parallella board. 

.. _bsp-prerequisites:

Prerequisites
-------------

.. rubric::  ARM toolchain  
 
Xilinx tools come with ``arm-xilinx-eabi-`` and ``arm-xilinx-linux-`` toolchains, either of them can be used.

If you do not need or want to use Xilinx tools, any other ARM toolchain (e.g. `Sourcery CodeBench lite edition <http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/>`_) can also be used for compiling the BSP. 

.. rubric:: U-Boot tools

U-Boot tools are required to generate a loadable image of the Linux kernel. 

On Debian/Ubuntu distributions this software can be obtained with: 

.. code-block:: bash 

   apt-get install u-boot-tools

.. todo:: OTHER DISTROS

.. _u-boot-build:

U-Boot
------

Getting the source
++++++++++++++++++

.. todo:: Change to gen1

Official U-Boot source code for the Parallella board can be found at: https://github.com/parallella/parallella-uboot on the ``parallella-gen0`` branch.
In order to get it use:

.. code-block:: bash 

   git clone https://github.com/parallella/parallella-uboot
   git checkout parallella-gen0

Building
++++++++

.. code-block:: bash 

   export ARCH=arm
   export CROSS_COMPILE=<your_toolchain_prefix> #e.g. arm-xilinx-eabi-
   export PATH=</path/to/your/toolchain>:$PATH
   make parallella_config 
   make -j<X> #X is typically no. of threads on your system +1

Linux
-----

Getting the kernel source
+++++++++++++++++++++++++

Official Linux kernel source code for the Parallella board can be found at: https://github.com/antmicro/linux-parallella on the ``parallella-linux3.9`` branch.
In order to get it use:


.. code-block:: bash 

   git clone https://github.com/antmicro/linux-parallella
   git checkout parallella-linux3.9        

Building the kernel
+++++++++++++++++++

.. code-block:: bash 

   export ARCH=arm
   export CROSS_COMPILE=<your_toolchain_prefix> #e.g. arm-xilinx-eabi-
   export PATH=</path/to/your/toolchain>:$PATH
   make parallella_defconfig
   make -j<X> uImage #X is typically no. of threads on your system +1
