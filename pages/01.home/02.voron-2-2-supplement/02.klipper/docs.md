---
media_order: unknown-2.png
date: '18:58 19-09-2019'
taxonomy:
    category:
        - docs
visible: true
---

!! Always check the latest Klipper documentation for feature updates: 
!! * [Klipper Docs](https://github.com/KevinOConnor/klipper/tree/master/docs)
!! * [examples.cfg](https://github.com/KevinOConnor/klipper/blob/master/config/example.cfg)
!! * [examples-extra.cfg](https://github.com/KevinOConnor/klipper/blob/master/config/example-extras.cfg)
 


## Content
[Toc]


## Config walkthrough

Latest stock config is found on Github:
[Voron Klipper Config](https://github.com/VoronDesign/Voron-2/tree/master/firmware/klipper/configurations)

!! Throughout this document it is assumed that you have succesfully installed Klipper following the Klipper install guides
!!
!!https://github.com/KevinOConnor/klipper/blob/master/docs/Installation.md
!!https://github.com/KevinOConnor/klipper/blob/master/docs/Config_checks.md


!!! Throughout this process - if you make changes to the config file - you must issue a firmware_restart command or restart the klipper service for it to take effect


### Items you need to update

Arduino paths                          [mcu](#MCU-definitions) section
 Thermistor types                      [extruder](https://www.voron.dev/home/voron-2-1/klipper#extruder-settings) and [heater_bed](https://www.voron.dev/home/voron-2-1/klipper#heated-bed-settings) sections
 See 'sensor types' list at end of file
 
 FSR switch (z endstop) location       [homing_override](#Homing-Override) section
 FSR switch (z endstop) offset for Z0  [stepper_z]() section
 Probe points                          [quad_gantry_level](#Quad-Gantry-Levelling-Settings) section
 Min & Max gantry corner postions      [quad_gantry_level](#Quad-Gantry-Levelling-Settings) section
 PID tune                              [extruder](#Extruder-Settings) and [heater_bed]https://www.voron.dev/home/voron-2-1/klipper#heated-bed-settings) sections
 Fine tune E steps                     [extruder](#Extruder-Settings) section


### MCU definitions 

Each RAMPS 1.4 board is placed onto an Arduino Mega controller. 
When you install Klipper you will flash the Klipper code onto both Arduinos. We need to find the USB paths of your Arduinos and tell Klipper where to find them on startup. 

You can do this two ways - by-id or by-path. by-id uses the unique serial number of your Ardunios to identify them. by-path uses the physical location of the USB port on your pi. It is preffered to use by-id, however if you decided to use a cheap clone they often do not have a serial chip installed - in this case you must use by-path and then be careful to always plug the X MCU and the Z MCU into exactly the same port on the pi if you ever need to change your wiring. 

You can find out the serial id using the following command in an SSH terminal: 

```
ls /dev/serial/by-id/
```

Example Output:
```
pi@voron-2023:~ $ ls /dev/serial/by-id
Arduino__www.arduino.cc__0042_55737313631351601271-if00 
Arduino__www.arduino.cc__0042_55737313631351603928-if00 
```

These are the ID's we need to insert into the config file. To determine which Arduino is which it is easiest to plug in the XYE Arduino first - do the 'ls' command - and then plugin the second Arduino and grab the second ID using 'ls'. 


```py
[mcu]
# Mcu for X/Y/E steppers
serial: /dev/serial/by-id/Arduino__www.arduino.cc__0042_55737313631351601271-if00
pin_map: arduino
restart_method: arduino
```

```
[mcu z]
# Mcu for Z steppers
serial: /dev/serial/by-id/Arduino__www.arduino.cc__0042_55737313631351603928-if00 
pin_map: arduino
restart_method: arduino
```


If you only see one listing when you have two arduinos connected - it is likely you do not have serial chips: 

```
pi@voron-2053:~ $ ls /dev/serial/by-id
usb-1a86_USB2.0-Serial-if00-port0
```

In this case you need to check /dev/serial/by-path/
It is a good idea to unplug any USB webcams or any other USB devices while you discover which port is which. 

```
pi@voron-2053:~ $ ls /dev/serial/by-path/
platform-3f980000.usb-usb-0:1.1.3:1.0-port0  
platform-3f980000.usb-usb-0:1.2:1.0-port0
```

You then use these values in the config - note the change from by-id to by-path:

```
[mcu]
# Mcu for X/Y/E steppers
serial: /dev/serial/by-path/platform-3f980000.usb-usb-0:1.1.3:1.0-port0
pin_map: arduino
restart_method: arduino
```

```
[mcu z]
# Mcu for Z steppers
serial: /dev/serial/by-path/platform-3f980000.usb-usb-0:1.2:1.0-port0 
pin_map: arduino
restart_method: arduino
```

!! Danger
!! If you use by-path you must always plug the same Arduino into the Same USB port.   Plugging it into a different port will change it’s path, possibly flipping your MCU X with MCU Z

### Printer definitions

In this section you set your maximum accelerations and velocity. The stock config is confgiured fast - so if you are facing issues - you can tweak these values lower and then increase them as you finish tuning your printer. These are the highest values that klipper will allow regardless of what you may have configured in your slicer. 

```
[printer]
kinematics: corexy
max_velocity: 350
max_accel: 3000
max_z_velocity: 50
max_z_accel: 350
square_corner_velocity: 10.0
```

Square corner velocity is defined as: 

   The maximum velocity (in mm/s) that the toolhead may travel a 90
   degree corner at. A non-zero value can reduce changes in extruder
   flow rates by enabling instantaneous velocity changes of the
   toolhead during cornering. This value configures the internal
   centripetal velocity cornering algorithm; corners with angles
   larger than 90 degrees will have a higher cornering velocity while
   corners with angles less than 90 degrees will have a lower
   cornering velocity. If this is set to zero then the toolhead will
   decelerate to zero at each corner. The default is 5mm/s.

## X and Y steppers (A and B drives)


In the stepper_x and stepper_y section you will define your steppers for the A/B drives on the gantry. 

It is useful when first configuring to test that you have the motors plugged in the right place on the ramps using the stepper buzz command. 

```
STEPPER_BUZZ STEPPER=stepper_x
STEPPER_BUZZ STEPPER=stepper_y
```

This will move each motor back and forth a few times. If you have the motors the wrong way around - you can either power off and switch the connections on the RAMPS board - or switch all the PIN definitions in the config to effectively 'swap' the motors. 

!! Danger
!!ALWAYS power off the printer when connecting or disconnecting stepper motors from the RAMPS board or you will damage the stepper driver chip


Next you need to check the movement. To begin with you do not want to run a home sequence as your motors might be moving in the wrong directions. To test direction - manually move the toolhead near the center of your bed. Then run the set_kinematics command to force the printer to the location you are at. 

```
SET_KINEMATIC_POSITION X=125 Y=125 Z=100
```

!!! You need to add the following into your config for set_kinematic_position to work
!!!
!!! ```
!!! [force_move]
!!! enable_force_move: true
!!! ```

Now you can use the jog controls in Octoprint to move the toolhead in X and Y - just use small moves here. 

![](https://i.imgur.com/UfE74q3.png?cropResize=200,100)



This diagram can help with troubleshooting the directions of your motors.

![](unknown-2.png)

If you need to swap motors or change directions you can physically swap the connectors or make changes in config. To invert the direction of a motor in the printer.cfg file - add or remove the '!' in front of the dir_pin definition. Run a RESTART command to load the new configuration.



```
dir_pin: !ar55
```

Once everything is moving in the right direction you will want to configure your endstops. Voron homes X/Y to MAX in the top right corner.

![](https://i.imgur.com/VrO6eaB.png)

If you have a larger build than 250x250 - adjust these values accordingly in both stepper_x and stepper_y:

```
position_endstop: 255
position_max: 255
```

First you should check that the toolhead can actually reach the switches without interference. Use the set_kinematcis command and slowly jog the toolhead to the right and confirm that you can hear the X endstop switch clicks at X-Max. 


Now do the same moving the toolhead back and confirm the Y endstop switch clicks at Y-Max. Y in particular you need to check that your wiring chains are not interfering with the movement at the back of the printer. Also - you may find that the rear left of the gantry hits the back before the rear right reaches the switch - this means you are not square and you need to adjust the belts in the x-carriage. (See [mechanics](https://hackmd.io/c/SkJV_Sw9V/https%3A%2F%2Fhackmd.io%2F-Zgitmj2TwKtQzoedzPIVw)) 

Confirm that your endstops are working using query_endstops in the Octoprint terminal 

```
Send: QUERY_ENDSTOPS
Recv: x:open y:open z:open
Recv: ok
```

Now press each endstop switch and run query_endstops and confirm it returns 'TRIGGERED'

```
Send: QUERY_ENDSTOPS
Recv: x:TRIGGERED y:open z:open
Recv: ok
```

If query_endstops returns 'TRIGGERED' when you are not pressing the pin and open when you do press it - invert the endstop_pin by adding or removing the '!'. You may wish to confirm your switch is wired as you intended. Usually that's normally closed.

```
endstop_pin: ^!ar2
```

If the state does not change when pressed - you need to check all your wiring and confirm that you are plugged into the correct endstop pins on the MCU X/Y board. 

!! You should also confirm that your FSR pin is triggering for Z-Min at this stage. You must also confirm that your Z motors are moving in the right direction. See [Z Steppers](#Z-Steppers)

Now we will do the first homing sequence. Get the toolhead near the center of the bed and raise the gantry high so you have time to stop it if something goes wrong. 

!! Be ready to press emergency stop or power off your printer at this point

Run G28 to start homing. First the toolhead should move right towards the X endstop switch, then move back towards the Y endstop switch and finally down towards the Z endstop switch.

!! At this point we have not configured the position of the FSR switch - so you will be manually pressing it to stop the gantry moving down

```
G28
```

!!! As the toolhead moves right - manually press the X endstop switch twice to stop it.

!!! As the toolhead moves back - manually press the Y endstop switch twice to stop it.

!!! As the gantry moves dowm - manually press the Z FSR endstop switch twice to stop it.
 
Once you are happy that the movement is stopping when the switch is pressed - run G28 again.

```
G28
```

This time let the toolhead move to X and Y endstop automatically.

!! You stil need to manually press the Z FSR endstop as the gantry moves down


Now that you are comfortable that X/Y is triggering we need to tune X-Max and Y-Max

Manually jog the toolhead to X=0 and Y=0 at the front left of the printer using Octoprint. Do this slowly as you may have values that mean the toolhead will hit the left of the printer before X=0 and the front before Y=0. 

If you find that X=0 is not far enough to the left - increase position_endstop and position_max accordingly - decrease it if you are going past the side of the bed. 

Do the same for Y=0 - you will want this as far forward as possible so that the toolhead can reach the FSR pin at the back of the printer. You may need to move your bed forward a little to make this work. 

You want the tool head as far to the front as possible without touching the front idlers when Y=0. 


Now that you have X and Y endstops configured you can configure the position of your FSR pin. See [Homing Override](#Homing-Override)

!! Remember - if you ever make adjustments to your endstops or change the belts significantly you will need to adjust your endstop positions and update the position of the FSR pin in homing_override



```
[stepper_x]
# B Stepper
step_pin: ar54
dir_pin: !ar55
enable_pin: !ar38
#   X on mcu_xye
step_distance: 0.0125
#   80 steps per mm - 1.8 deg - 1/16 microstepping
endstop_pin: ^ar2
#   X_MAX on mcu_xye
position_min: 0
position_endstop: 255
position_max: 255
homing_speed: 100
homing_retract_dist: 5
homing_positive_dir: true
```


```
[stepper_y]
# A Stepper
step_pin: ar60
dir_pin: !ar61
enable_pin: !ar56
#   Y on mcu_xye
step_distance: 0.0125
#   80 steps per mm - 1.8 deg - 1/16 microstepping
endstop_pin: ^ar15
#   Y_MAX on mcu_xye
position_min: 0
position_endstop: 255
position_max: 255
homing_speed: 100
homing_retract_dist: 5
homing_positive_dir: true
```

### Z Steppers

Each of the four z steppers are controlled independantly by klipper. It is important that the steppers are wired and configured in the correct order in a clockwise direction starting with front left. 

![](https://i.imgur.com/qaO6C1Y.png)

In the first stepper definition you will also set your FSR Z endstop position. 

![](https://i.imgur.com/Jm9Q4KI.png)

This tells klipper that when this switch is hit by the nozzle what the current Z position is (position_endstop). Some people refer to this as the Z offset which can be confusing - as it is the actual height of the nozzle when the switch is pressed. It does effectively dictate your z-offset but when adjusting this value - it is better to think of it as the distance the nozzle will travel to meet the bed. 

![](https://i.imgur.com/IjMyqWA.png)

So when you increase this value - your nozzle will be closer to the bed - when you decrease it - it will be further away. 

If your FSR pin head happens to be lower than the bed - you would set this to a negative value. 

When first setting this value start with a low value and work up so that you avoid accidently crashing into the bed. 

I also recconmend testing near the edge to begin with and then fine tune in the center when you are comfortable how the adjustments work. 

position_max: determines the absolutle maximum height the Z will be allowed to move. If you try to move higher than this either manually or in g-code - you will get a move out of rnage error. 

position_min: determines the lowest Klipper will allow the nozzle to go. This is a safety feature, but it is common to set this to a negative value so that you can baby step during a print below Z=0 if the enstop is configured slightly too high. 

Once you have this configured - confirm that your endstop is working using query_endstops in the Octoprint terminal 

```
Send: QUERY_ENDSTOPS
Recv: x:open y:open z:open
Recv: ok
```

Now press the FSR pin and run query_endstops and confirm it returns 'TRIGGERED'

```
Send: QUERY_ENDSTOPS
Recv: x:open y:open z:TRIGGERED
Recv: ok
```

If query_endstops returns 'TRIGGERED' when you are not pressing the pin and open when you do press it - invert the endstop_pin by adding or removing the '!'

```
endstop_pin: ^!z:ar18
```

If the state does not change when pressed - you need to check all your wiring and confirm that you are plugged into the correct endstop pin on the MCU Z board. 

Now you can check that the gantry is moving in the right direction. 
Start by using STEPPER_BUZZ and confirm each motor is moving and in the correct sequence clockwise from front left to front right. 

```
STEPPER_BUZZ STEPPER=stepper_z
STEPPER_BUZZ STEPPER=stepper_z1
STEPPER_BUZZ STEPPER=stepper_z2
STEPPER_BUZZ STEPPER=stepper_z3
```

Now we will confirm direction. Use set_kinematics to tell the printer where it is without homing.
```
SET_KINEMATIC_POSITION X=125 Y=125 Z=100
```

Now use the Octoprint jog controls to move the gantry up. Use 1mm steps very slowly as if any of your motors are moving in the wrong direction the gantry will twist. 

Watch each motor as you move up - if one of them is moving in the wrong direction - invert the direction by adding or removing '!' from the dir_pin setting.

```
dir_pin: !z:ar55
```





```
[stepper_z]
# Z0 Stepper - Front Left
step_pin: z:ar54
dir_pin: !z:ar55
enable_pin: !z:ar38
#   X on mcu_z
step_distance: 0.00250
#   400 steps per mm - 1.8 deg - 1/16 microstepping
endstop_pin: ^!z:ar18
#   Z_MIN on mcu_z
position_endstop: -0.2
position_max: 250
position_min: -2
homing_speed: 15.0
second_homing_speed: 3.0
homing_retract_dist: 3.0
homing_positive_dir: false
```



```
[stepper_z1]
# Z1 Stepper - Rear Left
step_pin: z:ar60
dir_pin: z:ar61
enable_pin: !z:ar56
#   Y on mcu_z
step_distance: 0.00250
#   400 steps per mm - 1.8 deg - 1/16 microstepping
```


```
[stepper_z2]
# Z2 Stepper - Rear Right
step_pin: z:ar46
dir_pin: !z:ar48
enable_pin: !z:ar62
#   Z on mcu_z
step_distance: 0.00250
#   400 steps per mm - 1.8 deg - 1/16 microstepping
```


```
[stepper_z3]
# Z3 Stepper - Front Right
step_pin: z:ar26
dir_pin: z:ar28
enable_pin: !z:ar24
#   E0 on mcu_z 
step_distance: 0.00250
#   400 steps per mm - 1.8 deg - 1/16 microstepping
```

### Extruder Settings 
```
[extruder]
step_pin: ar26
dir_pin: ar28
enable_pin: !ar24
#   E0 on mcu_xye
step_distance: 0.00180180
#   555 steps per mm - 1.8 deg - 1/16 microstepping (Mobius2)
nozzle_diameter: 0.400
#pressure_advance: 0.00
#   The amount of raw filament to push into the extruder during
#   extruder acceleration. An equal amount of filament is retracted
#   during deceleration. It is measured in millimeters per
#   millimeter/second. The default is 0, which disables pressure
#   advance.
#pressure_advance_lookahead_time: 0.010
#   A time (in seconds) to "look ahead" at future extrusion moves when
#   calculating pressure advance. This is used to reduce the
#   application of pressure advance during cornering moves that would
#   otherwise cause retraction followed immediately by pressure
#   buildup. This setting only applies if pressure_advance is
#   non-zero. The default is 0.010 (10 milliseconds).
filament_diameter: 1.750
max_extrude_only_distance: 780.0
#   This is set high to allow the load/unload filament macros to run
heater_pin: ar10
#   D10 on mcu_xye   
max_power: 1.0
sensor_type: NTC 100K beta 3950
sensor_pin: analog13
#   T0 on mcu_xye
smooth_time: 3.0
max_power: 1.0
control: pid
pid_Kp: 16.430
pid_Ki: 0.755
pid_Kd: 89.337
min_extrude_temp: 170
min_temp: 0
max_temp: 270
```



```
[probe]
# Inductive Probe
# Z height probe. One may define this section to enable Z height
# probing hardware. When this section is enabled, PROBE and
# QUERY_PROBE extended g-code commands become available. The probe
# section also creates a virtual "probe:z_virtual_endstop" pin. One
# may set the stepper_z endstop_pin to this virtual pin on cartesian
# style printers that use the probe in place of a z endstop.
pin: ^z:ar19
#   Z_MAX on mcu_z
x_offset: 0.0
y_offset: 25.0
#   Offset (in mm) for inductive probe Y direction from nozzle
z_offset: 0.00
#   Offset (in mm) for inductive probe in Z height
speed: 2.0
#   Speed (in mm/s) of the Z axis when probing. The default is 5mm/s.
samples: 4
#   Number of times to probe a point
sample_retract_dist: 4.0
#   How far to retract (in mm) from the probe point for multi-probe samples
```

```
[fan]
# Print cooling fan
pin: ar9
#   D9 on mcu_xye
max_power: 1.0
#   The maximum power (expressed as a value from 0.0 to 1.0) that the
#   pin may be set to. The value 1.0 allows the pin to be set fully
#   enabled for extended periods, while a value of 0.5 would allow the
#   pin to be enabled for no more than half the time. This setting may
#   be used to limit the total power output (over extended periods) to
#   the fan. If this value is less than 1.0 then fan speed requests
#   will be scaled between zero and max_power (for example, if
#   max_power is .9 and a fan speed of 80% is requested then the fan
#   power will be set to 72%). The default is 1.0.
#shutdown_speed: 0
#   The desired fan speed (expressed as a value from 0.0 to 1.0) if
#   the micro-controller software enters an error state. The default
#   is 0.
#cycle_time: 0.010
#   The amount of time (in seconds) for each PWM power cycle to the
#   fan. It is recommended this be 10 milliseconds or greater when
#   using software based PWM. The default is 0.010 seconds.
#hardware_pwm: False
#   Enable this to use hardware PWM instead of software PWM. The
#   default is False.
kick_start_time: 0.500
#   Time (in seconds) to run the fan at full speed when first enabling
#   it (helps get the fan spinning). The default is 0.100 seconds.
```

```
[heater_fan hotend_fan]
# Hotend fan
pin: z:ar9
#   D9 on mcu_z
max_power: 1.0
kick_start_time: 0.500
heater: extruder
heater_temp: 50.0
fan_speed: 1.0
```

```
[heater_fan controller_fan]
# Controller fan
pin: z:ar10
#   D10 on mcu_z
max_power: 1.0
kick_start_time: 0.500
heater: heater_bed
heater_temp: 45.0
fan_speed: 1.0
```

```
[heater_fan exhaust_fan]
# Exhaust fan
pin: z:ar8
#   D8 on mcu_z
max_power: 1.0
kick_start_time: 0.500
heater: heater_bed
heater_temp: 60.0
fan_speed: 1.0
```
### Heated Bed Settings
```
[heater_bed]
heater_pin: z:ar11
#   D11 (servo) on mcu_z
sensor_type: NTC 100K MGB18-104F39050L32
#   NTC 100K MGB18-104F39050L32 is for Keenovo thermistors
sensor_pin: z:analog15
#   T2 on mcu_z
smooth_time: 3.0
max_power: 0.75
control: pid
pid_Kp=47.690
pid_Ki=1.556
pid_Kd=365.338
min_temp: 0
max_temp: 110
```

Alternative way of controlling bed temperature is using watermark (bang-bang) algorithm instead of PID.

Watermark is that just a basic threshold with on/off control.

It needs just one parameter, max_delta (defaults to 2). 
This defines a temperature range where the heater will turn on/off. 
E.g. if you set it to 4 and your target bed temp is 90, the heater will turn on if your bed's temp is below 86 C and turn off if its above 94 C.

```
[heater_bed]
...
control: watermark
```

### Homing Override

Here you will configure the position of your FSR switch. You must configure your endstops before starting this process - as any changes to X or Y endstop position will move these values. 

Manually move the nozzle directly above the center of your FSR pin.

Issue M114 to see current position of the nozzle

```
Send: M114
Recv: X:179.000 Y:249.500 Z:0.000 E:0.000
Recv: ok

```

These X/Y value are what you will put into homing_override to move the nozzle above the FSR pin when homing:

```
G0 X179 Y249.5 F3600
```

```
[homing_override]
axes: z
set_position_z: 0
gcode:
   G90
   G0 Z5 F600
   G28 X Y
   G0 X179 Y249.5 F3600      
#  XY Location of the FSR Switch
   G28 Z
   G0 Z10 F1800
   G0 X125 Y125 Z20 F3600
```

### Safe Z Home (Homing Override alternative)

Safe z home does not rehome X,Y when homing Z.

homing_override requires the gcode to home all axes before homing Z.
Which means a G28 Z will result in all three axes being homed instead of just Z.

To enable safe z homing disable homing override and provide a X,Y coordinate of your Z endstop pin.

```
[safe_z_home]
home_xy_position: 175.5,253
z_hop: 5.0
```

### Quad Gantry Levelling Settings

```
[quad_gantry_level]
#   Put a moving gantry into plan with a fixed bed.  Must have 4 steppers on the gantry.
#   Use QUAD_GANTRY_LEVEL to level a gantry.
gantry_corners:
   -55,-7
   305, 320
#  List of X,Y coordinates describing the two opposing corners of the gantry.
#  The first entry corresponds to the front left motor, the second to the 
#  back right motor. See picture below.
points:
   25,0
   25,200
   225,200
   225,0
#  Probe points used for Quad Gantry Level.
#  The positions specified are "Nozzle positions". In a Voron 2.1 the probe
#  is offset by 25mm thus transforming 25,0 to a probe position of 25,25.
#  When modifying these make sure to keep at least 20mm distance to the
#  edge of the bed or any screws.
speed: 200
horizontal_move_z: 6
retries: 3
#  Retry the quad gantry level up to 3 times if the probed points
#  aren't within the specified retry_tolerance
retry_tolerance: 0.009
#  Repeat the quad gantry level if the tolarance is .01mm or larger.
```

![](https://i.imgur.com/ySB2oaj.png)

:::info
The gantry corner and probe points are depended on your printer size.

Voron 2.1 250mm Spec
```
gantry_corners:
   -55,-7
   305, 320
```
Voron 2.1 300mm Spec
```
gantry_corners:
   -55,-7
   355, 370
```
Voron 2.1 350mm Spec
```
gantry_corners:
   -55,-7
   405, 420
```
:::

### Miscellaneous

```
[display]
# RepRapDiscount 128x64 Full Graphic Smart Controller
lcd_type: st7920
cs_pin: z:ar16
sclk_pin: z:ar23
sid_pin: z:ar17
#   LCD connector on mcu_z
menu_timeout: 40
#   Timeout for menu. Being inactive this amount of seconds will exit the menu
#   Default is 0 seconds (disabled)
encoder_pins: ^z:ar33, ^z:ar31
click_pin: ^!z:ar35
kill_pin: ^!z:ar41


###   Macros   ###

[gcode_macro G32]
gcode:
    G28
    QUAD_GANTRY_LEVEL
    QUAD_GANTRY_LEVEL
    G0 X125 Y125 Z20 F6000
   
[gcode_macro PRINT_START]
#   Use PRINT_START for the slicer starting script - please customise for your slicer of choice
gcode:
    M117 Homing...
    G28                            ; home all axes
    G1 Z20 F3000                   ; move nozzle away from bed
    M117 Preheat (Print)
    M104 S0                        ; turn off hotend while waiting for bed to get to temp

[gcode_macro PRINT_END]
#   Use PRINT_END for the slicer ending script - please customise for your slicer of choice
gcode:
    M400                           ; wait for buffer to clear
    G92 E0                         ; zero the extruder
    G1 E-10.0 F3600                 ; retract filament
    G91                            ; relative positioning
    G0 Z1.00 X20.0 Y20.0 F20000    ; move nozzle to remove stringing
    TURN_OFF_HEATERS
    M107                           ; turn off fan
    G1 Z2 F3000                    ; move nozzle up 2mm
    G90                            ; absolute positioning
    G0  X125 Y250 F3600            ; park nozzle at rear
    M117 Finished!

[gcode_macro UNLOAD_FILAMENT]
gcode:
    M83
    G1 E10 F300
    G1 E-780 F1800
    M82

[gcode_macro LOAD_FILAMENT]
gcode:
    M83
    G1 E750 F1800
    G1 E30 F300
    G1 E15 F150
    M82

# Sensor Types
#   "EPCOS 100K B57560G104F"
#   "ATC Semitec 104GT-2"
#   "NTC 100K beta 3950"
#   "Honeywell 100K 135-104LAG-J01"
#   "NTC 100K MGB18-104F39050L32" (Keenovo Heater Pad)
#   "AD595"
#   "PT100 INA826"
```

###### tags: 'Voron', 'Klipper'
