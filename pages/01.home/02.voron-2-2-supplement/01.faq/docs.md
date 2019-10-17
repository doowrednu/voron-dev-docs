---
title: FAQ
date: '18:57 19-09-2019'
taxonomy:
    category:
        - docs
visible: true
---

# Voron FAQ

!! ## Always check Discord #FAQ for the latest updates 

[TOC]

Q) Should I read #official_sourcing_guide and #part_printing_guidelines, and watch  #help_videos ?

A) Yes

Q) Should I build a Voron?

A) Yes

Q: Will cad source files be released?

A: No planned date on the released of cad files, step files, or anything other than the stls

Q: What is the purpose of this server?

A: To help others build a voron, if you are not building a voron, the 3dp discord is a better place for you.

Q) How much does a Voron cost?

A) A 300^3 build will cost approximately $1,000 USD. The move from 300 to 350 is much more significant than 250 to 300.

Q) Can I scale the frame larger than 350^3?

A) Yes, but it will be more expensive, with a reduction in print quality, and much slower than the supported sizes. The machine was also never designed to support those sizes so you will run into structural issues. The VORON Design team does not endorse or support printers larger than 350 x 350 in the XY and 500 in the Z due to internal testing.

Q) Can I use these 4040 extrusions that I have from another project?

A) Yes, but be prepared to redesign nearly every part.

Q) Why does the Voron use only 2020 extrusions?

A) Because 2020 is enough for the application. It’s not a CNC machine and the strength to weight/stiffness ratio is more than ideal for 3DP where no side loads or cutting forces are imparted.

Q) What if I cannot print ABS reliably?

A) ABS+ is easier to print without warping even if you can only use a cardboard enclosure. Alternatively you can submit a request in #pif-discussion. There is likely a lead time until delivery. Use this time to submit your AliExpress orders and wait.

Q) What are the recommended accent parts?

A) The recommended accent parts are denoted with a "[a]_" in the filename. Many of these are still functional in nature and should still follow the recommended print settings.

Q) Where should I buy my parts from?

A) A large portion of parts come from AliExpress, Arrow, Mouser, Digikey and Misumi.

Q) Can I substitute *this part* for *that part*?

A) You are welcome to make substitutions as you see fit but this printer is not a race to be as cheap as possible. It is designed to compete with many other high-end printers will still being affordable. Substituting parts may also have other unintended side effects and cause compatibility issues. Please feel free to ask for advice before any substitutions are made.

Q) Why are the wiring recommendations so strict?

A) The wiring recommendations are made out of an abundance of caution to hopefully prevent electric shorts and fires.

Q) Do I need to get extrusions from Misumi?

A) No you can get them elsewhere but you may have issues with the slot profile. There are many different extrusion profile "standards" and the Misumi profile is what's recommended to deal with rail offsets, bolt head sizes, etc. as designed.

Q) What are the "TPW" and "LCP-RCP" options for the frame?

A) These are the options specified to have Misumi tap and drill the hex key access holes in your extrusions. You can remove these options and do this yourself to save ~$40 USD. You will need appropriate tapping tools, oil, and patience to do it correctly which may add back up to $40 when it's all complete.

Q) The HNTAJ5-5 nuts are expensive. Do I really need this many?

A) No, you can substitute ball spring post-assembly nuts from Ali just as easily in 99% of the manual. You ***need*** 16 narrow M5 t-nuts for the gantry ends. You can substitute all M3 nuts for ball spring post-assembly if desired. Short M5 nut locations

Q) What is a good crimping tool to use for terminating wires?

A) Engineer PA-09, PA-20, or PA-21. Yes, it's worth the cost.

Q) What is a good tool for grinding flats on the 5mm rods?

A) A Dremel or bench grinder work well. A flat file can also work fine. Use eye protection when working with high RPM power tools and fragile cutting discs.

Q) What are good brands for drivers to work with all of the fasteners?

A) Bondhus, Wiha, Wera

Q: Will full kits/hardware kits be avalable for purchase:

A:  There are no plans to sell kits of any kind.
 
Q: Does V2.1 support direct drive? 

A: There are no plans to support anything other than bowden extrusion, pressure advance helps with 99% of the downsides and the mobius3 can print tpu.

Q: Does the V2.1 support dual extrusion

A: There are no plans to support dual extrusion at this time.

Q: Can I use my V-Slot extrusions from another project

A: No, MGN9 Linear rails cannot sit properly on them, and fall into them.
 

Q: Can I use 0.9 degree stepper motors rather than the speced 1.8 degree motors. 

A: 
 - They would not be recommended for the Z and E motors. These are already geared which give them better resolution. Going to 0.9 degree motors would mean they would then need twice as many steps to go the same distance as a 1.8 degree motor. This combined with microstepping results in more cpu load on your boards and would then start to limit how fast you can drive these. 
 - They may have some beneficial affect on the XY axis (AB motors) which don't have the gearing that the Z and E assemblies have. Don't expect miracles though, filament variance is another factor that starts to be an issue as well when attempting small detail. 
- While 0.9 motor can be a bit quieter, from all accounts in an enclosed Voron the fans are likely to make most of the noise. So any noise benefit would be negligible(edited)
- Using the BOM spec ramps and klipper with 0.9deg steppers will limit your theoretical travel speeds greatly, just running 1/16 microstepping with 0.9deg XY motors and pressure advance with a 1.8deg stepper on the Extruder is enough to easily overwhelm the board.

Q: Why did you use A4988s instead of DRVs or TMC stepper drivers in the design?

A: 
 - A4988s provide the best cost/noise/performance ratio. When run in the voron they are also quiet enough to rival most TMC driven machines. TMC steppers also cool poorly and often overheat on ramps based systems due to the tiny copper pour on the stepstick PCB. 
 - We also do not need the amperage capacity of the DRV8825s. 
 - A4988s are also usually included with ramps kits, so its a win for everyone.
 
Q: Why do you recommend RobotDigg linear rails (chinese) vs a Hiwin or THK genuine rail?

A: 
 - We are not imparting cutting forces onto the carriages on the rails (ie: side loads), so the low preload and ok tolerances are good for this application
 - A Set of 7 rails is the cost of one genuine mid-grade THK rail.
 - Clean all of your rails, then grease them (there are numerous videos on youtube on how to do this). Pick your best rail for the X axis, 2 for the Y, use the worst on the Z

Q: What is the recommended wire for the cable chain

A: 
 - Striveday Silicone Wire, 20AWG for the Heaters and 24AWG for the remainder.

Q: What about the Clever3d PEI coated plate? It seems cool, and not having to apply the PEI sheet myself would be nice..

A: 
 - Several users have attempted them, and they are simply not workable with ABS. Testing showed they required a first layer temperature of at least 130C, which is approaching the failure temperature of the adhesive on the recommended Keenovo heater.
 - Their NON-PEI coated plates are a solid option though for those in Europe wanting to source a build plate.
 - If you own a PEI coated Clever3d bed, we recommend applying a sheet of your own PEI to it

Q: Why does the VORON not using IGUS products?

A:
 - IGUS bushings exhibit a phenomenon called stiction, therefore in small movements the bushings will actually stick instead of gliding, at small detail, this causes detrimental issues.
 - The amount of slop required is unfavorable, tolerances are quite poor.
 - Bushing life is low for the price when compared to Misumi and even standard chinese offerings.
 
Q: Using the number of washers shown in the manual, things are too tight or too loose 

A: Depending on where you sourced your washers they may have different tolerances. The VORON is designed around 1mm thick shim washers, you can buy either 0.5mm or 1mm thick ones and adjust accordingly. Standard washers are sized as follows: 1mm +0.2mm -0.00mm usually.
 
Q: Can you print voron parts using a volcano

A: VORON parts are designed with a 0.4mm nozzle in mind, it is not recommended to use a 0.4mm nozzle with a volcano, even though it is available

Q: Does the Voron Support the Super Volcano

A: No, its a case of shit design and metal fatigue waiting to happen: https://www.reddit.com/r/3Dprinting/comments/blqw6s/i_believe_there_was_some_initial_concern_about/
 

Q: Can I substitute MGN(insert rail size here) for MGN9H rails?

A: No, VORON V2s are not designed for anything but a MGN9H
 
Q: Why does the VORON not use TMC2100, 2130, or 2208 drivers

A:
 - The VORON specs 2A steppers at 12v to achieve its performance.
 - We are using the 2A spec OMC steppers purely for their holding torque capability. The characteristic of a low inductance/low coil resistance of the VORON spec motors is absolutely not inline with how these motors drivers operate. They are designed for high inductance steppers. 
 - If you really want to use TMCs just grab a genuine or cloned duet, you can put klipper on them now and they can drive high current steppers all day long with 2660s.
- If that's not an option you still want to use TMC stepsticks, add large heatsinks with active cooling and plan to significantly reduce travel and print speeds.
 
Q: Why doesnt the VORON have a chamber heater

A: There are a few reasons
 - Big company patents and lawyers
 - Joe burning his house down
 - Its really easy to screw up for someone that doesnt have great amounts of experience with properly controlling/mounting them
 - If we spec something and people cheap out, there will be fires involved
 
Q: What firmware does the VORON use?

A: Klipper: Read this before asking any and all questions: https://www.klipper3d.org/Overview.html

Q: I get error 
Option 'samples' is not valid in section 'quad_gantry_level' 
 when updating klipper to latest version
 
A: 

With the recent update both 
samples: 
 and 
sample_retract_distance: 
 need to be moved from the [quad_gantry_level] section to the [probe] section - you should also re-flash your arduinos and re-run 
/home/pi/klipper/scripts/install-octopi.sh

Q:   I don't understand how the position_endstop value relates to getting my nozzle height set properly.

A:   Think of the top skin of your print surface as Z0 and the height where your nozzle triggers the Z-Endstop as the offset value known as:
[stepper_z]
position_endstop: 

Q: Do I need to reterminate my steppers to match the manual or some other documentation?

A: No. There is not a "standard" wire color order for these parts. If you decide to reterminate to change connector types or whatever, be sure to use the same order as before. The spec motors also come with a datasheet or card so you can double check your work. You can also use a multimeter to find wire pairs in the motor by measuring continuity between leads. Each lead should have continuity to the other lead in its pair.

Q: What are the requirements to get a serial for my printer?

A: You need to have completed cable management above the deck plate, toolhead doors attached and closed, and a video of a print in progress showing the complete printer, including these details, submitted to the Voron subreddit.

Note: be sure to correctly flair your submission as a serial request or it may get overlooked. Mods try to process requests a couple of times a week so please be patient.
 
Q: I cannot find MIC6 Aluminum anywhere, is there something else I can use?

A: Yes, try to find the following, they are all the same thing, with a different trade name:
 - Mic 6
 - Alpase K100-S
 - Alca 5
 - Vista Metals ATP 5
 - Alimex 5083 (vendor is very important on this one)

Materials not supported, these are all not designed to take repeated heating and cooling cycles and will warp quickly:
 - 6061
 - 5052
 - 5053
 - 2024
 - 7### series not listed above
 - 5### series not listed above
  

Q: Do I need a screen on my printer ? 

A: No - many of us use tablets running octoprint in the browser or a plugin like Nautilus.
 
Q: Is the voron designed to print exotic materials?

A: 
- No
- A voron is made from printed abs parts and does not support being built with other materials.
- The voron does not have a heated chamber, therefore it is not designed to print exotic materials. Exotics such as peek require build chamber temperatures of over 100C to print properly, which will result in everything in the chamber self-destructing. Belts are good to 80C max, steppers around 90C, printed parts start deforming around 70C. 
- Watercooling is not supported because of the risk associated with it, leaks and mains voltage don't play well with each other. You need a separate cable chain to properly run the tubing which would not fit inside the machine (XY is mostly the issue, but there is an issue with the Z also).
- Adding a chamber heater would exceed the current requirements of a standard home circuit (with buffer), a standard voron can draw up to 10A easily from the wall during warmup. Adding a water pump and a chamber heater will easily push it near a 15a current draw, as internal testing has determined a we need approximately 700w of heating power to warm the chamber to 75C (per multiple ansys simulations).
 - The true recommended chamber temp for PEEK is 130C, for True Nylon or PC (not hybrids or blends) is 100C, below this you will see delamination. This basicaly equates to putting your printer in an oven, youd want the least amount possible of the motion system in the "hot zone" and a ton of insulation. VORON is currently not designed for this, and it is not planned to be.
- This machine is intended such that it can be built with "garden tools", chamber heaters violate this, and add unnecessary risk to the end user.
- We recommend you purchase an industrial printer designed for this purpose if you intend to print with these materials so you dont end up with a fire on your hands.
 
Q: Is the SKR Mini E3 an acceptable controller?

A: No, it has serious design flaws: A poor motor driver layout, small copper cooling area, and potential USB compatibility issues. The LCD header does not support the spec GLCD.
 
Q: How do I set my VREF on my A4988s?

A: https://e3d-online.dozuki.com/Guide/VREF+adjustment+A4988/92

Q: What should I set my VREF to?

A: 0.9 VREF is a good starting point, assuming you have the standard chinese reference resistor value of 100, this only applied to a RAMPs board as that is spec. Some people have had to run as high as 1.2 VREF

Q: Ive never built a printer, what should I do?

A: Build either VORON model 100% to spec, you get a config that will work pretty close to out of the box, a wiring diagram, etc. It will also be easier for us to help you

Q: Will you be supporting the new e3D Hermes Extruder?

A: No, Use a Mobius3dot1, its better, and cheaper
 
Q: Does the voron support dual extrusion, dual hotends, or toolchangers?

A: No, there are no plans at this time, if you want dual extrusion, pickup a pallette 


