Building the FPGA bitstream and FSBL
====================================

This section describes preparation of the hardware project. It is basing on exemplary project containing Processing System (PS) configuration and its connection to Programmable Logic(PL). Epiphany link and HDMI IP cores are placed in PL and connected with PS via AXI bus. See following subsection for details. 

Prerequisites
-------------

To view or modify exemplary project Xilinx PlanAhead (version 14.4 or newer) is required. See :ref:`xilinx` for details.

Project
-------

Whole project was implemented in Verilog (top level and Epiphany link) and VHDL (HDMI IP core) using planAhead software (version 14.4). Project can be divided into two main parts: 

#. CPU embedded project (containing PS configuration and HDMI IP core)
#. Epiphany link (providing interface between CPU and Epiphany chip) 

Both parts are instantiated in top level file (parallella_7020_top.v). 

.. _ready_to_use:

Project structure
-----------------

#. Parallella__7020_top.v - Top file 
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

.. csv-table::
   :header-rows: 1

   Peripheral, connection
   UART1, MIO 8..9
   QSPI, MIO 1..6
   SD1, MIO 10..15
   USB0, MIO 28..39
   USB1, MIO 40..51
   I2C0, EMIO
   Enet0, MIO 16..27

Note that I2C0 peripheral is connected to EMIO IO controller, thus attached to the PL part of the Zynq chip. The only purpose of doing so is to be able to route this interface through PL to desired chip pins. To do so signals from this interface (SCL and SDA pins) must be set as external ports in the Embedded Processor project. 

In order to connect Epiphany link to the CPU it is required to derive AXI bus interface outside the Embedded processor project. To do so following interfaces has to be enabled and set as external ports:

#. Master AXI interconnect (M_AXI_GP1)
#. High performance AXI slave interconnect (S_AXI_HP1)

For audio data transfer it is required to enable DMA (DMA0) controller and connect it to the axi_spdif_tx peripheral in Progammable Logic.

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

HDMI support is provided by IP core from Analog Devices (`Analog Devices GitHub <https://github.com/analogdevicesinc/fpgahdl_xilinx>`_). IP cores needed for HDMI support are located in cf_lib folder. Peripherals used in the Parallella Exemplary project, their purpose and connections are listed below.
 
#. axi_clkgen (v1.00a) - programmable reference clock for HDMI transmitter. It is connected to main AXI bus and provides reference clock for axi_hdmi_tx_16b peripheral. It requires 200MHz input clock (FCLK_CLK2 in exemplary project)
#. axi_vdma - dma controller for video data. It is connected to main AXI bus as a slave and to secondary as master. 
#. axi_hdmi_tx_16b - video signals generator for ADV7513 chip. It generates video synchronization signals (HSYNC and VSYNC), pixel clock and delivers video data. It is connected to main AXI bus and requires reference clock with proper frequency for chosen resolution. Video data is transferred to this peripheral with DMA. 
#. axi_spdif_tx - digital audio signal generator. For correct work it is required to deliver 12.288135 MHz clock signal to this component. It is connected to main AXI bus and audio data is delivered using DMA. 

To provide proper clock signal to spdif peripheral Xilinx clock generator IP core can be used.

Epiphany link
+++++++++++++

.. todo:: found an issue with the Epiphany link, difference in signal between gen0 and gen1 - fix

.. _FSBL:

FSBL
----

The First stage boot loader code is generated from Xilinx Software Development Kit.

.. todo:: More info about creating and building it can be found here and here (reference to Xilinx guide [if any])
