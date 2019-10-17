---
title: Electronics
published: true
date: '18:58 19-09-2019'
taxonomy:
    category:
        - docs
visible: true
---

# Voron Electronics
## Content
[TOC]
## Basics
Generally, follow the manual when building the Voron, when in doubt either check the [Voron Manual Breakdown](https://www.voron.dev/home/voron-2-2-supplement/2-2-manual-breakdown) or ask for advice in the [Voron Discord](https://discord.gg/wfwFKuf)

A few useful tips:
Depending on your level of expertise it may be useful to practice crimping and soldering before wiring everything up. Bad crimps are a major source of problems later on an sometimes hard to identify!
Safety
Mains cables should be properly dimensioned. Use at least 20 AWG for mains connections, even better would be 18 AWG.
Buy a quality SSR, do not source one from china or your house will catch on fire (see Official Sourcing Sheet)!
Add a 115-125°C thermofuse to your bed heater as an additional safety measure.
Dont run your bed above 105°C, this greatly reduces the lifespan of the adhesive holding it onto the build plate
Connect ground to your bed!
Connect a ground to your frame!

Connectors
The recommended MicroFit 3.0 connectors are specced up to 5A and should be used for all connections to the stepper motors, hotend and limit switches
The dupont connectors are used for connections to the RAMPS board
Adding molex connectors to all stepper motors is useful. You can disconnect motors while moving the gantry manually to avoid damage to the RAMPS board.
Use a 3pin MLX connector (see the official sourcing guide) to allow your bed to be removable without disconnecting from the SSR directly. The thermistor can use a 2pin microfit connector.

Cables
The specced silicone wire has been chosen because:
It is very high strand count, which means it has higher fatigue life
Silicone insulation withstands heat and is more flexible which is good in high movement applications
It is less prone to snagging on other wires or the walls inside the cable chain
Check you cable loom before installing the tape chains. You need at least:
8x 24AWG wires in the z chain (2 steppers)
4x 24AWG wires in the y chain (endstops)
7x 24AWG (probe, fans, thermistor)
2x 20AWG in the x chain (heater) - do not downsize these, they are oversized for safety reasons
You may add additional wires to your cable chains as a replacement in chase of wire breaks. However, these cables will be heated/cooled and moved around as much as your live cables and may be broken already when you need them!
X/Y endstop connector can be strapped to the bottom of the joint. Just make sure the cable is long enough, be sure to install the cover to keep any prints that fly off from bonding to your endstops
Buy some cable holders (e.g. with adhesive) to organize your cables in the electronics compartment. Keep in mind the adhesive fails over time due to exposure to above room temperature
Cables can be hidden in the rails. Optionally print cable covers for the rails


* Display
    * Some display extension boards have loose connectors. Ensure that all pins have a solid connection
    * Sometimes EXT1 and EXT2 have to be exchanged
    * Sometimes EXT1 and EXT2 connectors are on 180 degrees from the correct orientations and thus are keyed the wrong way for the cables
    * Some displays come with very dark backlight. You may have to adjust contrast on the LCD. Depending on the display this may require desoldering
* RPi Power
    * Some step-down boards do not supply enough amps to reliably run the RPi. Sometimes you get undervoltage warnings, sometimes printing is flaky with strange errors (MCU communiation, etc.)
    * The best option to ensure stable power is a separate 5V PSU (e.g. RSP-XX-5V) connected to the USB (either via stripped USB cable or soldered to the pads [PP2 5V and PP3/PP4/PP5 GND](https://www.raspberrypi.org/documentation/hardware/raspberrypi/schematics/rpi_SCH_3bplus_1p0_reduced.pdf)). Do NOT supply power through GPIO pins. This circumvents fuses and is more likely to damage your pi.
* Motor Positions
    * Z motor positions start in the front left corner and advance clock wise.
    * The "A Drive" motor is located on the right hand side.
    * ![](https://i.imgur.com/YbFplPe.png)


## Sourcing
Follow the [Sourcing Guidlines](https://hackmd.io/P_XMpxHGRCe9__FdwCZJsg?view#Sourcing) and the [Official Sourcing Sheet](https://docs.google.com/spreadsheets/d/1QS80JOHcgBIABJrAD7sIsVr5nARIzHsFrJnI4P4npgs/) to buy Voron parts.

!! The sourcing sheet is copy protected on purpose to prevent outdated copies. This way the development team can guarantee an up-to-date sheet


Make sure to buy Arduino Megas with ATMega16U2 Chips to ease your Klipper installation. Many cheap china clones use CH340 with a standard ID that will make Klipper installation less convenient.


## Offical controller setup

The official supported controller setup is a Raspberry Pi 3B+ with 2x RAMPS 1.4 on Ardunino MEGA boards connected via USB. Furthermore, 7x A4988 drivers are used.

Deviate from this setup at your own risk! A few options used in the community are described below, but official support is limited to the standard RAMPS setup.

## Useful videos
### Solder tutorials
* [How to solder the diode](http://www.eaavideo.org/detail/videos/feature/video/713336874001/soldering-a-resistor-or-diode-to-a-wire)
### Tools
* [Tools and equipment overview](https://youtu.be/GYyvWiW_t78) {%youtube GYyvWiW_t78 %}

### Voron electronics
* [V2 Build and electronics](https://youtu.be/FZJyOXPUiRk) {%youtube FZJyOXPUiRk %}
* [Hotend and Bed](https://www.youtube.com/watch?v=8C4jtj0OuE4&feature=youtu.be) {%youtube 8C4jtj0OuE4 %}
* [Limit switch fun](https://www.youtube.com/watch?v=fKyn02Ntz7A) {%youtube fKyn02Ntz7A %}
* 
### Controller and Motors
* [Motors and controller](https://www.youtube.com/watch?v=tPl-X-tKCnU) {%youtube tPl-X-tKCnU %}
* [Measuring vref and setting current](https://www.youtube.com/watch?v=E6RWysjlaP4) {%youtube E6RWysjlaP4 %}
* [RAMPS 24V conversion](https://www.youtube.com/watch?v=3eRuHNw-Uz8) {%youtube 3eRuHNw-Uz8 %}


## Useful modifications 
### Hall effect endstop
A contact free tuneable alternative to the standard KW10 switches as X/Y endstops is the [Hall Effect XY Endstop](https://github.com/mzbotreprap/VORON/tree/master/PCB/XY%20Endstop).

The hall effect endstop is entirely optional, but may provide more room in the build room if tuned properly. You will have to source and build these on your own. 

### 24V Voron
Standard RAMPS 1.4 mod includes
* Replace the poly fuses with 24V fuses. Multiple ways:
    * Individual fuses, e.g., car fuses with appropriate amperage
    * Simply brigde the connections and add an inline fuse to the ramps board. Voron 2.1 uses a mains heater, individual fuses are therefore not required
* Remove diode D2 to protect the Ardunio Mega board

With 24V you either need to source
* 24V fans or use a step-down / additional 12V PSU for 12V fans
* 24V heater for the hotend
* (Optional) an appropriate step-down to power the RPi

! 24V fans for hotend cooling (e.g. Delta, Sunon) and parts cooling (e.g. 3dsway via Aliexpress) are available. However, no 24V electronics fan alternative is known!



### Alternative controller setups
Instead of 2x RAMPS with Arduino MEGAs some Vorons run alternative MCUs either for 32bit or a tighter package 
* SKR 1.3
* MKS sBASE
* MKS Gen 1.4


!! Alternative controllers are not supported by the dev team! You are on your own to figure out the klipper config. Still, community members may share their configuration.


### Unsupported controller setups
* SKR Mini E3 : This board has serious design flaws. The board does not feature this spec GLCD for LCD panel. Poor motor layout and small copper cooling.

###### tags: `Voron`,`Electronics`