# CR-6_Klipper
A repository for Klipper firmware details for the Creality CR-6 printers.

Installation description:

Head over to: https://3dprintbeginner.com/how-to-install-fluiddpi-on-raspberry-pi/

Follow the installation instructions and install the latest version of FluidPI. Writing to the SDcard (or USB SSD drive if you have that setup) as described. Unitpp you reach the "Install KIAUH" section. Then first run the necessary security items as per below:

You will need putty or a similar program (I prefer MobaXterm as it is an SSH client and remote file system in one).
After starting up the PI FIRST run 
  Sudo raspi-config
 and set a new passowrd (1 - S3), optionally chenge the system name
 It is handy to immediately activate the SPI port for later use with the acceleration sensor (3 - P4)
 reboot the PI with "sudo reboot"
 
 After reboot run:
  sudo apt update && sudo apt upgrade -y
 to update the PI to the latest security levels. You will want to run this command from time to time to keep your PI updated.
 reboot the PI with "sudo reboot"
 
 After finishing the security items continue with the setup as described on 3dprintbeginner
 
 The KIAUH screen should look like this when everything went correctly (without running anny commands from this interface). One can optionally install other functions but this is not needed for a fully functional Klipper install.
 /=======================================================\
|     ~~~~~~~~~~~~~~~~~ [ KIAUH ] ~~~~~~~~~~~~~~~~~     |
|        Klipper Installation And Update Helper         |
|     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~     |
\=======================================================/
/=======================================================\
|     ~~~~~~~~~~~~~~~ [ Main Menu ] ~~~~~~~~~~~~~~~     |
|-------------------------------------------------------|
|  0) [Upload Log]     |       Klipper: Installed: 1    |
|                      |        Branch: master          |
|  1) [Install]        |                                |
|  2) [Update]         |     Moonraker: Installed: 1    |
|  3) [Remove]         |                                |
|  4) [Advanced]       |      Mainsail: Not installed!  |
|  5) [Backup]         |        Fluidd: Installed!      |
|                      | KlipperScreen: Not installed!  |
|  6) [Settings]       |                                |
|                      |          DWC2: Not installed!  |
|  v3.1.0-7            |     Octoprint: Not installed!  |
|-------------------------------------------------------|
|                        Q) Quit                        |
\=======================================================/
Perform action:

now exit out of the KIUH directory:
  cd ..

Now we need to copy the example printer definition to the main printer.cfg file. All active config files will be in the /home/pi/klipper_config directory. The example files for the different printers are in /home/pi/klipper/config directory
"/home/pi/klipper/config/printer-creality-cr6se-2020.cfg" is for the 4.5.2 board
"/home/pi/klipper/config/printer-creality-cr6se-2021.cfg" is for the 4.5.3 and ERA boards
"/home/pi/klipper/config/generic-bigtreetech-skr-cr6-v1.0.cfg" is for the BTT CR6 board

run:
  cp /home/pi/klipper/config/generic-bigtreetech-skr-cr6-v1.0.cfg /home/pi/klipper_config/printer.cfg
 replace then board file with the appropriate one for your mainboard
 
 
 
 
 IN PROGRESS
