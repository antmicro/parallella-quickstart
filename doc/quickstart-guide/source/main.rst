Introduction
============

This document describes how to run Ubuntu Linux with HDMI support on the Parallella board.
The current version is an early draft for internal use of Adapteva, but it is meant as a seed for the full document describing the process for all Parallella users.

Version information
-------------------

.. csv-table::
   :header-rows: 1

   Author,Content,Date,Version
   Michael Gielda,Initial version,2013-08-26,0.1.0

Procedure
=========

#. Download https://www.dropbox.com/s/vyu55tenc736sfw/parallella_7020_600_hdmi.tar.bz2, generate the bitstream and flash it onto the board (I suppose you know how to do that; this will not be present in the final manual).
#. Download the files from https://www.dropbox.com/sh/1hn6ih5dmb2gr9c/cK01Et5HH3 and copy them onto the BOOT partition of the SD card.
#. Unpack http://releases.linaro.org/12.11/ubuntu/precise-images/ubuntu-desktop/linaro-precise-ubuntu-desktop-20121124-560.tar.gz to a USB drive (note: I have not verified this download link but I guess this is the one we used, I will confirm with Karol tomorrow).
#. Insert the SD card to the board, connect an USB hub to the board, then a keyboard, mouse and the abovementioned USB drive to the hub.
#. Connect a display to the micro HDMI connector with a custom Parallella HDMI-microHDMI converter cable ( ;) ).
#. Power up the board.
#. ...
#. Profit!
