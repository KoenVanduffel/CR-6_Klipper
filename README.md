# CR-6 Klipper configuration helper
A repository for Klipper firmware details for the Creality CR-6 printers.
This repo focusses on using Klipper and the Fluidd front end. In this configuration there is no need for OctoPi - Fluidd is way more responsive than OctoPi.

This has been tested on a BTT CR6 board by myself. The 4.5.2, 4.5.3 and ERA boards have been reported to work correctly.

Bear in mind that the CR-6 screen will not work (yet) with Klipper. The Fluid interface runs on any PC or mobile device in a standard web browser. As an alternative to the CR-6 screen, KlipperScreen can be used. KlipperScreen runs on a series of Raspberry pi touch screens or on an old phone. Please check out [the KlipperScreen GitHub](https://github.com/jordanruthe/KlipperScreen) for detailed info.

# What is Klipper?
Klipper is a complete package to run your 3D printer consisting of 3 main tools:
* Klippy: the service running the printer. Klippy interpretes the gcode, produces move commands and sends these to the printer. Klippy is where the heavy lifting is actually done.
* Moonraker: The broker. Moonraker handles the communication between Klippy, the visual front end(s) and hardware on the rPi.
* Fluidd/Mainsail/KlipperScreen: The Klipper display and User Interface. These all 3 do more or less the same (you only need one of them but can run all 3 if wanted). They are the user interface where we put/upload our gcode files, do all calibrations, view a bed mesh and so on.

## Installation description:

Download the FluidPI or Mainsail image from the FluiddPI or Mainsail GitHub repo:
* https://github.com/cadriel/FluiddPI/releases/
* https://github.com/mainsail-crew/mainsailOS/releases/

The image is burned to an SDcard as described here:
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
## Find and copy the CR6.cfg file and the Printer.<motherboard>.cfg file specific to your printer

Copy the printer definition for your motherboard from this repo to the pi and rename it, "klipper.cfg". 
* Select the appropriate .cfg file for your motherboard and copy it to /home/pi/klipper_config on the pi
* Rename that file from "printer.<motherboard>.cfg" to "klipper.cfg"

Copy the CR6.cfg file from this repo to the pi, as that contains the Klipper configuration instructions specific to our CR6 printer.  
* For all motherboards copy CR6.cfg to /home/pi/klipper_config on the pi
* Do NOT rename that file

## Now make the actual firmware to be flashed to the printer mainboard

For your convenience, precompiled firmwares have been provided in the firmware directory. For now the BTT CR6 is tested and in use by myself. The stock Creality one is only confirmed to connect properly to my old 4.5.2 board. I have not actually printed with it. Please leave feedback if you use them, so I can confirm they are working.

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

The make command will build the actual firmware to be flashed to the printer and output the file /home/pi/klipper/out/klipper.bin

Download klipper.bin, rename it to firmware.bin and write it to an SD card

<span style="color:red">

## The SDcard HAS TO BE maximum 16 GB formatted as FAT32 with 4096 bits sector size.

## Any other format and the processor simply cannot read the SD card.
</span>

A micro-SDcard in an SD adapter works perfectly fine, as long as the formatting is correct.

The last step is to give Klipper the address of the USB connection. To obtain the address in the ssh terminal, run:

```bash
ls /dev/serial/by-id/*
```

the output should look like this (NOTE: the actual address will differ on yours):
```bash
/dev/serial/by-id/usb-Klipper_stm32f103xe_36FFD8054255373740662057-if00
```

Copy the actual text of that line ton your system to the [mcu] section of Klipper.cfg to **replace** the string present there

## From this point on, you should have a working Klipper installation, which now needs to be verified fully functional.

Proceed to the functionality checks as described on the Klipper site: https://www.klipper3d.org/Config_checks.html

