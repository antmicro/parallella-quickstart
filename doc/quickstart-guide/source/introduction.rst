Introduction
============

This document is a quickstart guide describing how to run Ubuntu 12.04 :abbr:`LTS (long-term support)` Linux on the `Parallella board <http://www.parallella.org/>`_.

The :doc:`fpga` section describes how to build the FPGA bitstream with HDMI support.
The :doc:`bsp` section describes how to build the bootloader, kernel and OS image (filesystem).
The :doc:`usage` section describes how to put all the necessary elements onto the Parallella and run the Linux image.

While the first two chapters of the guide feature information how to build your own software/fpga stack from scratch, you can :doc:`proceed straight to last chapter <usage>` if you download precompiled binaries, as described in the :ref:`appropriate section of that chapter <binaries>`.

If you do want to change the software/fpga stack you are running, you will also need Xilinx tools:

.. _xilinx:

Xilinx tools
------------

#. PlanAhead is required to view and reimplement exemplary project. Version used for creating exemplary project is 14.4, thus at least this version (or newer) is required to work with. If you do not plan to build your own hardware project you do not need this tool. 

#. Xilinx Software Development Kit (SDK) is required for generating and building the First Stage Boot Loader (FSBL). If you do not want to make any changes to the haconfiguration (which replacement of FSBL you do not need this tool. 

#. Xilinx Microprocessor Debugger (XMD) is required to connect with the Zynq CPU via JTAG. If you do not plan do load or debug software this way you do not need this tool.

#. Bootgen is required for preparing a bootable image for the Zynq CPU. If you do not plan to change default bootloader you do not need this tool.

.. todo:: WRITE ABOUT OPENOCD!!!!

If any of the abovementioned cases applies to you, you need to download and install `Xilinx ISE Design Suite <http://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/design-tools.html>`_.
Select "Embedded edition" as the installation type.

.. note:: Xilinx software is not free and needs extra licensing.

Version information
-------------------

.. csv-table::
   :header-rows: 1

   Author,Content,Date,Version
   Michael Gielda,Initial version,2013-08-26,0.1.0
   Karol Gugala,Software guide draft,2013-10-01,0.2.0
   Michael Gielda,Corrections,2013-10-09,0.2.1
   Karol Gugala, Hardware guide draft, 2013-10-14,0.3.0
