Usage
=====

This chapter covers how to prepare the necessary boot images and media based on binaries either built as described in the previous chapters (necessary only if you intend to change the hardware configuration) or downloaded as :ref:`described below <binaries>`.

Precompiled boot images are also available from the same source.

.. _binaries:

Precompiled binaries
--------------------

.. todo:: Reference binaries repository, describe how to download binaries. What about the filesystem binary?

Repository file list:

* fsbl.elf - compiled First Stage Boot Loader
* u-boot.elf - compiled U-Boot 
* uImage - Linux kernel image (u-boot loadable format)
* devicetree.dtb - compiled device tree for the Parallella board 
* parallella.bit - Zynq bitstream 
* parallella.bit.bin - Zynq bitstream (u-boot loadable format)

.. _bootgen:

Preparing a Zynq boot image
---------------------------

This section describes preparation of a Zynq boot image that can be written into the onboard flash memory of the Parallella board. 
A Zynq boot image can be created with the bootgen tool (see :ref:`xilinx`). Bootgen requires a ``.bif`` input file, which is just a text file describing boot image layout. An example ``.bif`` file is listed below:

::

   the_ROM_image:
        {
        [bootloader]/path/to/fsbl.elf
        /path/to/u-boot.elf
        }

With this configuration, a simple boot image containing only the first and second stage bootloader (FSBL and U-Boot) can be built.
This is done as follows:

.. code-block:: bash

   bootgen -image /path/to/image.bif -o i parallella.bin

The resulting file (``parallella.bin``) is a bootable image that can be programmed into the Parallella onboard flash memory. More information about using bootgen and creating Zynq boot images can be found in `Zynq-7000 All Programmable SoC Software Developers Guide <http://www.xilinx.com/support/documentation/user_guides/ug821-zynq-7000-swdev.pdf>`_.

Bootloader deployment
---------------------

There are two ways of running new bootloader software on the Parallella board:

#. Program the flash with a new Zynq boot image file see :ref:`flashing`.
#. Download and run the bootloader via JTAG connection see :ref:`jtag`.

.. _jtag:

Using JTAG
++++++++++

Prerequisites
~~~~~~~~~~~~~

* :ref:`xilinx`
* Xilinx Platform Cable

.. note:: JTAG deployment requires the Xilinx Platform Cable to be connected to the Parallella board. 

This method allows you to run new software on the Parallella board without reflashing it, or recover it after flashing with an improper boot image. 

1. The first step of the JTAG deployment procedure is running Xilinx Microprocessor Debugger tool. It is able to connect to Zynq CPU and provides a gdb server. Moreover, using this tool you are able to program the Zynq chip with a new configuration file, but it isn't always necessary (if not, the ``fpga`` command in the following procedure can be skipped).

The important step when setting the JTAG connection is the proper configuration of the Zynq chip (especially when it was flashed with an improper boot image). This is done by running tcl procedures from ``ps7_init.tcl`` script (TODO: ref to repository). The ``stub.tcl`` script sets the Zynq CPU into debug mode. 

   .. code-block:: tcl

      connect arm hw
      fpga -f </path/to/your/>bitstream
      source </path/to/your/>ps7_init.tcl
      ps7_init
      init_user
      source </path/to/your/>stub.tcl
      target 64

2. After configuring the Zynq chip, a software application (e.g. U-Boot) can be loaded onto it. This can be done either with xmd:

   .. code-block:: tcl

      dow </path/to/your/>u-boot.elf
      con
     
   or via gdb:

   ::

      target remote localhost:1234
      file </path/to/your/>u-boot.elf
      load
      c

.. _flashing:
   
Programming the flash
+++++++++++++++++++++

.. warning:: Reprogramming the flash with an incompatible Zynq boot image may result in breaking the Parallella board. Moreover, during flashing, a stable power supply must be assured. 

.. note:: If the board was programmed with an improper boot image or there was some other problem during flashing, it still can be bring up using JTAG (see :ref:`jtag`).

The U-Boot delivered with Parallella can be used to re-flash the board. The binary available from the repository (:ref:`binaries`) or built (:ref:`u-boot-build`) from github source also has this functionality. If you changed the default bootloader and it does not provide this feature, you can still run the default official Parallella U-Boot via JTAG (:ref:`jtag`) or load it with your current bootloader. 

To re-flash the Parallella board using the official U-Boot follow these steps:

#. Remove the SD card from the slot.
#. Power up the board - U-Boot should start, and the lack of and SD card will prevent it from booting Linux.
#. Put the SD card into the slot.
#. Run the following commands in the U-Boot prompt.

   * Initialize the mmc subsystem 

     ::

        mmcinfo 

   * Load the Zynq boot image from the first partition (FAT-formatted) into RAM (make sure the image file is present on the SD card)
     
     ::

        fatload mmc 0 0x4000000 <boot_image_name> 

     .. warning:: Be careful about the fatload address; if an address outside RAM is given (e.g. 0x40000000 instead of 0x4000000), the command will hang without a warning.

   * Initialize the SPI flash subsystem 

     ::

        sf probe 0 0 0

   * Erase the entire flash memory 

     ::

        sf erase 0 0x1000000 

     .. note:: This process can take a long time to complete, do not interrupt it.
   
   * Program the flash memory 

     ::

        sf write 0x4000000 0 0x$filesize 
     
     .. note:: The $filesize variable holds the size of the previously loaded file, so if you had loaded some other file in U-Boot in the meantime, this will not be the size you want and you have to provide the right value by hand.

#. Power cycle the board.

.. note:: The boot image length is displayed when the file is loaded into RAM from the SD card.

.. _booting:

Booting Linux
-------------

This section discusses a few (out of the many possible) ways of booting Linux on the Parallella board. If you are interested in general boot procedure of Zynq based device refer to `Zynq-7000 All Programmable SoC Software Developers Guide <http://www.xilinx.com/support/documentation/user_guides/ug821-zynq-7000-swdev.pdf>`_.

Booting from SD card / USB drive
++++++++++++++++++++++++++++++++

Copy ``uImage``, ``parallella.bit.bin`` and ``devicetree.dtb`` onto the first partition of a >= 2GB SD card (FAT-formatted), insert card into the board and power it up. Unpack the Linux root file system onto the USB drive or the second partition of SD card (ext formatted).

.. note:: Remember to set the proper boot device in the Linux kernel bootargs (``/dev/sdaX`` for USB drive boot or ``/dev/mmcblk0pX`` for SD card) 

Default Boot sequence
+++++++++++++++++++++

Default Boot sequence on the Parallella board is as follows:

#. After power-up, the internal Zynq BootROM finds the boot image in onboard flash, copies the FSBL from it into the On-Chip RAM and runs it.
#. FSBL finds the boot image on the onboard flash, copies U-Boot from it into RAM and runs it.
#. U-Boot searches the first (FAT formated) partition on the SD card for the Linux kernel image (uImage), devicetree (devicetree.dtb) and Zynq configuration (parallella.bit.bin). If found they are copied into RAM.
#. U-Boot configures the Zynq chip, and boots Linux kernel passing the devicetree to it.
#. The Linux kernel boots into the rootfs according to bootargs passed in the devicetree.

.. todo:: formating the SD card/USB stick
