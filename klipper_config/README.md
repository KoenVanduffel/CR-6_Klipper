# The Klipper configuration files for the Creality CR-6 SE
I assume that the Creality CR-6 Max has teh same config except for the size of the bed. Anyone willing to make and check it? I will put the config up here as well.

There are a few main configuration files we have to make:
* printer.cfg
    This file holds the main printer physical config items and is kept as closely to the Kipper repo one as possible. The main change for the BTT CR6 board is the addition of some items for the strain gauge system (I dont understand why they have been taken out, they claim for universality but this board is specifically made for the CR-6 series...)
* CR6.cfg
    The remaining setup of the stain gauge plus CR-6 specific macro's
* generic.cfg
    Generic stuff and macro's
