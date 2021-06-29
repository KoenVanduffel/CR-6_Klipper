# CR-6 Klipper configuration helper
A repository for Klipper firmware details for the Creality CR-6 printers.
This repo focusses on using Klipper and the Fluidd front end. In this configuration there is no need for OctoPi - Fluidd is way more responsive than OctoPi.

## Installation description:

Download the FluidPI image from the FluiddPI GitHub repo:
* https://github.com/cadriel/FluiddPI/releases/

The image is burned to an SDcard as described here:
* https://www.raspberrypi.org/documentation/installation/installing-images/

In the Raspberry pi Imager click **Chose OS** - **Use Custom** and select the just downloaded image. The rest of the install procedure is the same as on the raspberry page.

Setup WiFi if needed:
* https://www.raspberrypi.org/documentation/configuration/wireless/headless.md

The default user and passowrd are **pi** and **raspberry**. We need to change the default password.

Use MobaXterm (or equivalent) as it is an SSH client and remote file system in one to change the password and copy a few files onto the PI.

For people not used to work with Raspberry PI's https://www.raspberrypi-spy.co.uk/2018/12/remote-access-pi-using-mobaxterm/ has a good description how to get into the PI.

After starting up the PI FIRST run :
```bash
 sudo raspi-config
          set a new passowrd (optiion 1, S3), optionally change the system name
 sudo reboot
```

Now we need to copy the printer definition from this repo to the main printer.cfg file. Next to the printer.cfg file also the CR6.cfg file needs to be copied as it contains the specific things of the CR-6 style printers.

Select the config file appropriate for your motherboard and copy it to /home/pi/klipper_config

Rename the file to klipper.cfg

For all motherboards copy CR6.cfg to /home/pi/klipper_config

## Now we can make the actual firmware to be flashed to the printer mainboard

```bash
CD klipper
sudo service klipper stop
make menuconfig
          select microcontroller - STM32 - processor model STM32F103 (is the default)
          Bootloader - 28KiB
          communication interface USB (is the default)
          GPIO pins to set at micro-controller startup !PA14
```

The screen should now **exactly** look like this:

![image](https://user-images.githubusercontent.com/13643644/123483020-6a823c80-d606-11eb-8dfc-3924ef9c4a7f.png)

```bash
          Q and save the configuration
make
```

The make command will build the actual firmware to be flashed to the printer and output the file /home/pi/klipper/out/klipper.bin

download klipper.bin, rename it to firmware.bin and write to an SD card

<span style="color:red">

## The SDcard HAS TO BE maximum 16 GB formatted as FAT32 with 4096 bits sector size.

## Any other format and the processor simply cannot read the SD card.
</span>

A micro-SDcard in and SD adapter works perfectly fine as long as the formatting is correct.

The last step is to give Klipper the address of the USB connection. To ontain the addres in the ssh terminal run:
```bash
ls /dev/serial/by-id/*
```

the output should looke like this:
```bash
/dev/serial/by-id/usb-Klipper_stm32f103xe_36FFD8054255373740662057-if00
```

Copy this to the [mcu] section of Klipper.cfg and replace the string present there

You have to use a Linux aware text editor. The standard Windows tools will add the windows line ends which don't work on Linux. My favorite tool is Notepad++ which is a free download  https://notepad-plus-plus.org/downloads/

## From this point you should have a working Klipper installation.
You can proceed to the functionality checks as described on the Klipper site: https://www.klipper3d.org/Config_checks.html

## A few words to the Klipper way of handling z_offset.

Klipper defines z_offset at the point where it has measured the bed relative to the actual/real bed position. In contrast to Marlin defining it as an offset the nozzle needs to move in order to be at the actual bed position.

This means that z_offset as defined in the [probe] section of printer.cfg is a negative value with principaly the same absolute value as in Marlin, only negative.

The default z_offset has been set at -0.25 to be aconservative value and will hence lead to the printhead being too far from the bed. Baby step the nozzle down untill the correct distance is observed and correct the z_offset by subtracting the baby step value from the z_offset

Hence is you baby stepped down to -0.05 mm the z_offset becommes -0.25 - -0.05 = -0.20 mm

Something to get used to when comming from Marlin.

 ## IN PROGRESS
