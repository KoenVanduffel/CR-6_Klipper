# CR-6_Klipper
A repository for Klipper firmware details for the Creality CR-6 printers.

## Installation description:

Head over to: https://3dprintbeginner.com/how-to-install-fluiddpi-on-raspberry-pi/

Follow the installation instructions and install the latest version of FluidPI. Writing to the SDcard (or USB SSD drive if you have that setup) as described. Untill you reach the "Install KIAUH" section. Then first run the necessary security items as per below:

You will need putty or a similar program (I prefer MobaXterm as it is an SSH client and remote file system in one).
After starting up the PI FIRST run :
* sudo raspi-config

and set a new passowrd (1 - S3), optionally chenge the system name
It is handy to immediately activate the SPI port for later use with the acceleration sensor (3 - P4)
reboot the PI with 
* sudo reboot
 
After reboot run:
* sudo apt update && sudo apt upgrade -y

to update the PI to the latest security levels. You will want to run this command from time to time to keep your PI updated.
reboot the PI with "sudo reboot"
 
Finish the security items continue with the setup as described on 3dprintbeginner
 
The KIAUH screen should look like this when everything went correctly (without running anny commands from this interface). One can optionally install other functions but this is not needed for a fully functional Klipper install.

![image](https://user-images.githubusercontent.com/13643644/123164303-1004a700-d473-11eb-9999-e2fdb3fe8a28.png)


now exit out of the KIUH directory:
* cd ..

Now we need to copy the example printer definition to the main printer.cfg file. All active config files will be in the /home/pi/klipper_config directory. The example files for the different printers are in /home/pi/klipper/config directory
* "/home/pi/klipper/config/printer-creality-cr6se-2020.cfg" is for the 4.5.2 board
* "/home/pi/klipper/config/printer-creality-cr6se-2021.cfg" is for the 4.5.3 and ERA boards
* "/home/pi/klipper/config/generic-bigtreetech-skr-cr6-v1.0.cfg" is for the BTT CR6 board

run:
* cp /home/pi/klipper/config/generic-bigtreetech-skr-cr6-v1.0.cfg /home/pi/klipper_config/printer.cfg
replace the board file with the appropriate one for your mainboard
 
Now we need to edit the printer.cfg file
cd /home/pi/klipper_config/
nano printer.cfg
[in the stepper_z] section chenge endstop_pin:... to endstop_pin: probe:z_virtual_endstop (if not already there)
IN the [mcu] section add: 
 restart_method: command
Just below the comments section add:
 [include CR6.cfg]
 [include generic.cfg]

These 2 files will house the CR6 specific and generic setup items. At a later stage we can allways add extra's here. Putting all edits there keeps our printer.cfg file clean.
Now we are ready to build the actual printer firmware.

* CD klipper
* sudo service klipper stop
* make menuconfig
* select microcontroller - STM32 - processor model STM32F103 (is the default) 
* Bootloader - 28KiB 
* communication interface USB (is the default)
* GPIO pins to set at micro-controller startup !PA14

The screen should now look like this:

![image](https://user-images.githubusercontent.com/13643644/123483020-6a823c80-d606-11eb-8dfc-3924ef9c4a7f.png)

* Q and save the configuration
* make

this will build the firmware and output the file /home/pi/klipper/out/klipper.bin
download klipper.bin, rename it to firmware.bin and write to an SD card
### The SDcard HAS TO BE maximum 16 GB formatted as FAT32 with 4096 bits sector size. Any other format and the processor simply cannot read the SD card.
A micro-SDcard in and SD adapter works perfectly fine as long as the formatting is correct.
 
 ## IN PROGRESS
