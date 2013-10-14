Building the FPGA bitstream and FSBL
====================================

This section describes preparation of the hardware project. It is basing on 

Prerequisites
-------------

planAhead - tool required to view and reimplement exemplary project. It is part of Xilinx ISE Design suite :ref:`xilinx`. Version used for creating exemplary project is 14.4, thus at least this version (or newer) is required to work with. 

Project
-------

Whole project was implemented in Verilog using planAhead software (version 14.4). Project can be divided into two parts: 

#. CPU embedded project (containing HDMI IP core)
#. Epiphany link (providing interface between CPU and Epiphany chip) 

.. _ready_to_use:

Project structure
-----------------

#. Parallella_top.v - Top file 
#. Parallella.v - Epiphany link
#. System_stub.v - CPU embedded project wrapper  

Getting the project
+++++++++++++++++++

Clone from Github  

.. todo:: Repository address 

.. _hadware_configuration:

Processing System Configuration 
-------------------------------



Cortex A9 configuration
+++++++++++++++++++++++

Cortex-A9 Peripherals and connections:

#. Uart
#. QSPI
#. SD
#. USB
#. I2C
#. Ethernet
#. Master AXI interconnect 
#. High performance AXI slave interconnect 

.. note:: Note that CPU configuration isn't written to bitstream, thus Processing System is not configured during bitstream download process. It can be only done with software - generally with :ref:`FSBL`.

.. warning:: I2C is used during boot process to set proper voltage in power management chip. Since I2C is routed through Programmable Logic it must be assure that bitstream loaded by FSBL has this feature. If the proper voltage won't be set during boot the Parallella board will consume more power, which may lead to overheating.  

Clock configuration
+++++++++++++++++++

Zynq input clock frequency is 33.333333 MHz, while CPU clock ratio is set to 6:2:1. Clock configuration are listed in below tables: 

.. rubric:: CPU clocks

.. csv-table::
   :header-rows: 1

   Device,Clock source,Clock Frequency [MHz]
   CPU,ARM PLL,666.666666
   DDR,DDR PLL,400.000000
   QSPI,ARM PLL,200.000000
   Ethernet,IO PLL,125.000000
   SD,IO PLL,50.000000
   UART, PLL,50.000000

.. rubric:: PL clocks 

.. csv-table::
   :header-rows: 1

   Clock name,Clock Source,Clock Frequency [MHz],Peripherals driven
   FCLK_CLK0,IO PLL,100.000000,AXI interconnect 
   FCLK_CLK1,IO PLL,200.000000,AXI Video DMA 
   FCLK_CLK2,IO PLL,200.000000,HDMI reference clock 
   FCLK_CLK3,IO PLL,40.000000,Epiphany Link

Programmable Logic Configuration
--------------------------------

HDMI support
++++++++++++

HDMI support is provided by IP core from Analog Devices (link to github). 

Epiphany link
+++++++++++++

.. todo:: found an issue with the Epiphany link, difference in signal between gen0 and gen1 - fix

.. _FSBL:

FSBL
----

The First stage boot loader code is generated from Xilinx Software Development Kit.

.. todo:: More info about creating and building it can be found here and here (reference to Xilinx guide [if any])
