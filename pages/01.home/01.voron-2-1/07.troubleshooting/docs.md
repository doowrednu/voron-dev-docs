---
title: Troubleshooting
date: '18:59 19-09-2019'
taxonomy:
    category:
        - docs
visible: true
---

# Troubleshooting

## Firmware Errors

**Print halted due to "Heater [name] not heating at expected rate"** - This is likely due to PID tuning not being run correctly. Make sure to run a PID tuning *with the fans running* and save those values. Run the fans at 100% and try again. You may also find you need to have a silicone sock on the hot end due to how much power the fans can have in relation to how much the heater cartridge can maintain the temperature. Try lowering the fan speed to around 40%, add a silicone sock, or check your wiring if the issue persists. A 12v v6 should be able to handle ~250° with the part fans running.

## QUAD_GANTRY_LEVEL Issues
**QGL fails after too many attempts or gives an error about the probed points range increasing** - There is a known issue in 2.1.x printers that causes signal noise on the Z probe when it is sharing power with the hot end heater. Try running a wire along the Bowden tube from the +V of the probe to one of the +V rails on the PSU. Also run a -V from the probe to the PSU in the same way for the best results. Then try to home and QGL again. If the issue persists, the probe may be bad. If you have a spare, try swapping.

## Print Artifacts / Issues

**Z Banding** - If the banding occurs at a consistent interval, check to see if your Z belts are tensioned properly, Z idlers rotate correctly and are perfectly circular, and Z idler brackets are square to the frame.

**Layer Shifts** - Make sure AB belts are properly tensioned, all idlers spin properly, and all of the non-idler pulleys have a set screw registered against the ground part of the shaft. Make sure to use a thread locker.

**Print lifts from bed** - Make sure your Z_ENDSTOP_CALIBRATE has been performed and that you get a good result with the "paper test" or a 0.1mm feeler gauge. If the filament can be moved easily by hand when it has been laid down during a print, redo this process and move the Z down by ~0.01mm until the issue goes away. If your prints have the "elephant foot" effect (lowest layer sticks out further than the rest) you likely need to clean or replace your PEI as it shouldn't have to be squished that far to have good adhesion.

**Print warps or has one area of the print lift from the bed** - use the same process as the "Print lifts from bed" issue, and make sure your cooling fans are only running at or below 25% during prints. Ideally you want to start them at a later layer, but even with ABS you want a little bit of cooling during prints due to the Voron having a stationary bed.

**Parts are incorrectly dimensioned after print** - Chances are it's due to belt tension and/or pulleys in some way. Verify that nothing has come loose, make sure your AB belts are evenly tensioned, etc.

## How do I remove my PEI sheet?

DocJeeves's method:

You will need isopropyl alcohol (IPA, 90% or better purity)
I get a razor blade under the PEI sheet at a corner (or other likely spot), and work a fair bit of it up. Then I grab that lifted PEI corner with a pair of pliers, and pull. Usually the whole PEI sheet will come free, but large chunks of adhesive will remain. I spray that all down with 90% rubbing alcohol (70% may work? Never tried it), and walk away.
I come back, and the adhesive is now milky white, and comes off with no fuss with a razor blade

Another method is to put in the freezer, this makes it much easier to remove.

## Voron causes the lights to flicker in my house

This is caused by crappy wiring in your house + the bed heater being on. By default the heater is using a PID algorithm to keep its temperature at the set value -- this switches the heater on and off quickly (a bigger VORON has 700W+ mains heater; a 300mm V2 can pull 6-8 Amps at peak load, which can overload a circuit if there is too much else going on), this causes the flicker in your home electrical network. 

**Klipper**
To solve it change the bed heater control to `watermark`:
In the `[heater_bed]` section change the following:
```
control: watermark

# (defaults to 2). This defines a temperature range
# where the heater will turn on/off. e.g. if you set
# it to 4 and your target bed temp is 90, the heater
# will turn on if your bed's temp is below 86 C and
# turn off if its above 94 C.
max_delta: 2
```
You can remove the `pid_Kp, pid_Ki, pid_Kd` parameters from here, they are only needed for the `pid` control algorithm. 


If trying to fix these things using Klipper doesn't work, move the Voron to an outlet that is on a circuit with less on it. 