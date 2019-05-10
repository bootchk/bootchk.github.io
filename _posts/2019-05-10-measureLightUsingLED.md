---
layout: post
title: "Measuring light using an LED"
date: 2019-05-10
---

You can measure light using an LED.
You can use the same LED as an LED and as a light sensor, by clever use of GPIO pins.
This reports an experiment, my experience using a particular LED, the Cree C566C.

To measure light, you reverse bias the LED, which charges its internal capacitance, then count how long it takes to discharge.  The current that the LED generates as a solar cell discharges the capacitance.
(There are other techniques, e.g. using an ADC.)

Using GPIO pins, each leg of the LED uses a GPIO pin.
When using the LED as a light source, you forward bias by configuring both GPIO pins as output, first high and second low. 

```
positive --->|--- negative
```

When using the LED as a light sensor, you first reverse bias by configuring both GPIO pins as output, first low and second high.

```
negative --->|--- positive
```

(the LED will not light up.  It takes an infinitesimal time to charge up as a capacitor.)
Then you configure the second pin as an input, and wait for it to go low.

LED's and solar cells are similar.
They both exhibit the photoelectric effect.
But each is optimized for one use.
A solar cell won't emit much light (but does, and sometimes that is used to test them.)
An LED generates current very inefficiently (but does, and you CAN measure it.)

LED's and solar cells are both diodes.
It seems strange, but a solar cell generates current in the opposite direction from how you usually expect current to flow through a diode.
It is also strange that, when you use a blocking diode to keep a battery from discharging through a solar cell that is charging it,
you have two diodes back to back:   ---|<-->|---

You can count the time to discharge the LED capacitance (for a pin to go low):
  * using loop iterations
  * using interrupts from sleeping (more power efficient) and a counter
  
I found that there is significant difference between LED models, and between instances.  If you want accuracy, you might need to calibrate each LED as installed, or test each LED before installing (to weed out outliers.)  Vendors of LED's do NOT characterize them for use as light sensors.  LED's also have lenses that gather light.  The C566 has an asymmetrical lens, with about 30 degrees field of view.

Some results, not rigorous, for the time for an LED to discharge its reverse biased capacitance.

Light level | lux | mSec
--- | --- | ---
Direct sun | 100k | not measured
Bright sky (shaded) | 10k | not measured
Overcast day/drafting light | 1k | 5
Home interior | 100 | 50
Unlit basement | 10 | 500 (0.5 seconds)
Full moon night | 1 | 2k (2 seconds)

I tested using an MSP430.  My light meter was a cheap model "HS1010" not really capable of measuring less than 1 lux.

I am mainly interested in detecting day and night through a window.
I want to use an LED to save parts.
Less than 10 lux is night.
I don't know what effect city streetlights and ambient city sky glow will have.

Using the MSP430FR2433 in low power mode 3 uses about 1uA.  So I can detect night using only 1uA * 500 mSec of energy.

I have read that the reversed biased capacitance may be around 50 pico farads.  I could estimate the area of the LED surface, make a wild guess at an LED's current per area when used as a solar cell, and do some calculations.  The result should confirm the above results.  But I haven't done that.






  


