# Here is Help for Installing and Configuring Klipper on CR6 printers

PLEASE NOTE: 
* This GitHub repository provides pre-compiled resources and "how-to" guideance sufficient for you to find, install and configure Klipper firmware on Creality CR-6 printers.  
* This is NOT an in-depth tutorial on Klipper itself. You may need to supplement the information here and research answers to some questions elsewhere, before being able to understand and use some elements on this site.
* YouTuber NERO3D is one reliable source of more detailed information and tutorials for installing and configuring Klipper. This tutorial of his for installing the Fluidd/Klipper combo on an Ender3 V2 is a good example: https://www.youtube.com/watch?v=gfZ9Lbyh8qU (NOTE, though, that NERO3D uses [Balena Etcher](https://www.balena.io/etcher/) instead of the [Raspberry Pi Imager](https://www.raspberrypi.com/news/raspberry-pi-imager-imaging-utility/), to flash Fluidd to his SD.)

This repo focusses on using Klipper with the Fluidd front end. 

With this configuration, there is no need for OctoPi - Fluidd is much more responsive than OctoPi.

These resources have been tested on a CR6-SE printer with a BTT SKR CR6 board. The 4.5.2, 4.5.3 and ERA boards have been reported to work correctly with these resources.

Bear in mind that the CR-6 screen will not work (yet) with Klipper. 

The Fluid interface runs on any PC or mobile device in a standard web browser. 

As an alternative to the CR-6 screen, KlipperScreen can be used. KlipperScreen runs on a series of Raspberry pi touch screens or on an old phone. Please check out [the KlipperScreen GitHub](https://github.com/jordanruthe/KlipperScreen) for detailed info.

# What is Klipper?
Klipper is a complete package to run your 3D printer.

Unlike Marlin and the Marlin-based Community Firmware:
* The parts of Klipper that convert the gcode into printer instructions run on a separate host processor (like a Raspberry Pi), not on the printer motherboard
* Klipper makes a binary file that you do flash to the motherboard, but that firmware is only required to implement the printer instructions given to it by Klipper, over the USB serial interface.
* Klipper does not (yet*) communicate with the touchscreen hardware, so that touchscreen becomes "irrelevant" and unused, when you are running Klipper. (*We are, however, working on a DWIN display project that we hope will restore some functionality to that screen for the CR6Community members who migrate to Klipper.  Stay tuned!)

The Klipper package is comprised of 3 main tools:
* Klippy: the service running the printer. Klippy interpretes the gcode, produces move commands and sends these to the printer. Klippy is where the heavy lifting is actually done.
* Moonraker: The broker. Moonraker handles the communication between Klippy, the visual front end(s) and hardware on the rPi.
* Fluidd/Mainsail/KlipperScreen: The Klipper display and User Interface. These all 3 do more or less the same (you only need one of them but can run all 3 if wanted). They are the user interface where we put/upload our gcode files, do all calibrations, view a bed mesh and so on.

## Where to Download Klipper:
Download the FluidPI or Mainsail image of the Klipper package from the FluiddPI or Mainsail GitHub repo:
* https://github.com/cadriel/FluiddPI/releases/
* https://github.com/mainsail-crew/mainsailOS/releases/

## Where to Install Klipper:
**NOTE: For now, at least, this repo assumes you will install the Mainsail or Fluidd Klipper package on a Raspberry Pi.
If you intend to install Klipper on a different host, you will need to research the particulars of how that is done, elsewhere.**

Once you have Klipper installed and configured on the Pi, you must also "make" a binary file to flash to your printer's motherboard(s*) (*Klipper can support connecting multiple motherboards to the same Pi and the same printer)  The pi and Klipper will control the printer, via serial communications over the USB cable, with the bin file on the motherboard.

## How to Install the Fluidd or Mainsail Klipper package onto the Pi:

You can burn the FLuidd or Mainsail Klipper image to an SDcard usng the Raspberry Pi Imager application, as described here:
* https://www.raspberrypi.org/documentation/installation/installing-images/

In the Raspberry pi Imager click **Chose OS** - **Use Custom** and select the just downloaded image. The rest of the install procedure is the same as on the raspberry page.

Setup WiFi if needed:
* https://www.raspberrypi.org/documentation/configuration/wireless/headless.md

The default user and password are **pi** and **raspberry**. We need to change the default password.

Use MobaXterm (or equivalent) as it is an SSH client and remote file system in one to change the password and copy a few files onto the PI.

For people not used to work with Raspberry PI's https://www.raspberrypi-spy.co.uk/2018/12/remote-access-pi-using-mobaxterm/ has a good description how to get into the PI.

After starting up the PI FIRST run :
```bash
 sudo raspi-config
          set a new password (option 1, S3), optionally change the system name
 sudo reboot
```

```
The latest Raspberry Pi imager V1.7.1 and higher lets us set the username and password from the imager.
Also SSH can be enabled from the imager. This is done through the Settings gearwheel in the bottom right corner.
Mainsail can now be downloaded and installed directly through the Raspberry Pi Imager
```
## Find the applicable CR6.cfg file and the Printer._motherboard_.cfg files in [the Klipper_config folder of this repository](https://github.com/KoenVanduffel/CR-6_Klipper/tree/main/klipper_config)

In the klipper_config folder of this repo, you will find the following files:
* CR6.cfg - contains the Klipper software configuration information specific to our CR6 printer.  
  Use that file for all motherboards.
* Multiple files with the name Printer._motherboard_.cfg
  , where _motherboard_ is the name of the motherboard supported by that particular file (e.g. printer.Creality-4.5.3.cfg is the file to use if you have either the 4.5.3 or the 1.1.0.3-ERA motherboard from Creality.)  Use ONLY the printer._motherboard_.cfg named for your motherboard.

##  Install those two cfg files onto the pi
 
Copy CR6.cfg to /home/pi/klipper_config on the pi
Do NOT rename that file

Copy the appropriate printer._motherboard_.cfg file to /home/pi/klipper_config on the pi
Rename that file from "printer._motherboard_.cfg" to "klipper.cfg"

## Now make the Klipper firmware.bin file that you will flash to the printer mainboard

_NOTE: For your convenience, precompiled firmwares have been provided in the firmware directory. For now the BTT CR6 is tested and in use by myself. The stock Creality one is only confirmed to connect properly to my old 4.5.2 board. I have not actually printed with it. Please leave feedback if you use them, so I can confirm they are working._

For all stock creality boards, set the following config:
```bash
cd klipper
sudo service klipper stop
make menuconfig
            select microcontroller - "STM32"
            processor model "STM32F103" (is the default)
            Bootloader - "28KiB"
            Communication interface "Serial (on USART1 PA10/PA9)"

```

For the BTT CR6 board:
```bash
            tick "enable extra low-level configuration options"
            select microcontroller - "STM32"
            processor model "STM32F103" (is the default)
            Bootloader - "28KiB"
            communication interface USB (is the default)
            set GPIO pins to set at micro-controller startup to "!PA14"
```

The screen should now look **exactly** like this for the 4.5.2, 4.5.3 and ERA boards:

![image](https://user-images.githubusercontent.com/13643644/125346549-2e670f80-e35a-11eb-8940-d584d0bb70d7.png)


The screen should now look **exactly** like this for the BTT CR6 board:

![image](https://user-images.githubusercontent.com/13643644/123483020-6a823c80-d606-11eb-8dfc-3924ef9c4a7f.png)

```bash
          Q and save the configuration
make
```

## Copy the bin file output by the "make" command to an SD Card, and flash it to the printer
 
The make command will build the actual firmware to be flashed to the printer and will output the file as klipper.bin in the pi directory /home/pi/klipper/out/

Download klipper.bin, rename it to firmware.bin and write it to an SD card

<span style="color:red">

## The SDcard used to flash the motherboard MUST be formatted as FAT32 with 4096 bits sector size.

## Any other format and the processor simply cannot read the SD card.
</span>

A micro-SDcard in an SD adapter works perfectly fine, as long as the formatting is correct.

## Obtain the USB connection address and update the [mcu] section of Klipper.cfg
 
The last step is to give Klipper the correct address for the USB connection. 
To obtain the address in the ssh terminal, run:

```bash
ls /dev/serial/by-id/*
```

the output should look like this (NOTE: the actual address will differ on yours):
```bash
/dev/serial/by-id/usb-Klipper_stm32f103xe_36FFD8054255373740662057-if00
```

Replace the placeholder string in the [mcu] section of Klipper.cfg with actual output string displayed in your terminal 

## From this point on, you should have a working Klipper installation, the full functionality of which you should now verify.

Proceed to the functionality checks as described on the Klipper site: https://www.klipper3d.org/Config_checks.html

