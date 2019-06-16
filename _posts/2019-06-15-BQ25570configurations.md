---
layout: post
title: "Fundamental configurations of BQ25570 energy harvester"
date: 2019-05-10
---


Texas Instrument's BQ25570 chip is an energy harvester.

You can configure it several ways, re where do you connect the load and the storage?
(Not discussing how you also configure various voltage thresholds using resistor nets.)

Discussing supercap storage, not a battery.  (Not discussing what kind of storage to use.)

Read "Fast charging a supercapacitor from energy-harvesters"
http://www.edn.com/design/power-management/4422103/Fast-charging-a-supercapacitor-from-energy-harvesters
This post elaborates that discussion.

My next post will discuss a more specific example, 
using TI's spreadsheet to size the super capacitor and solar cells for a particular configuration.

Background
---

The BQ25570 has two converters:
   - charging (boost) converter, step-up to pin VBAT
   - regulating (buck) converter, step-down to pin VOUT
Thus you can have two output rails.

The basic configurations:
   1. Solar cell and blocking diode (no BQ25570 at all)
   2. Supercapacitor on VSTOR pin, load on VSTOR and VOUT
   3. Supercapacitor on VBAT pin, a. load on VSTOR and VOUT b. load on VOUT only
   4. Supercapacitor on VBAT pin, load on VOUT
   5. Supercapacitor and load on VOUT pin

The considerations are:
   - how fast it coldstarts and continues charging
   - how regulated the out voltages are
   - how easy is it to find appropriate solar cells
   - how well the capacitor storage is used
   - how efficient energy is harvested (MPPT)
   - what light conditions are accomodated

Configuration 1 Solar cell and blocking diode (no BQ25570 at all)
---

See the referenced paper.

The voltage out is unregulated. 
 
The solar cell can operate inefficiently (off MPP) much of the time.

A voltage drop is suffered across the blocking diode.

It constrains your choice of solar cells to those whose Voc is less than the Vmax of your system.

It charges fast, there is no coldstart of the energy harvester.

I have implemented this with an MSP430FR2433 and two IXYS KXOB22-04x3 solar cells in series generating Voc of 3.8V.

Configuration 2 Supercapacitor on VSTOR pin, load on VSTOR and VOUT
---

This is the configuration of some BQ25570 breakout boards, e.g. MikroElectronika MIKROE-1814 (except that does not bring VSTOR off the board conveniently so you can connect a load.)

Same characteristics as for configuration 3.


Configuration 3 Supercapacitor on VBAT pin, a. load on VSTOR and VOUT or b. load on VOUT only
---

It can coldstart very slowly, since in coldstart:
   - boost efficiency is about 10% 
   - the BQ clamps the solar cell to 0.3V which can be far from its MPP
   - lasts until the largish supercap on VBAT is charged to Vchgen==1.8V

You can charge the capacitor to 5V, using its full capacity.

Once you get past coldstart, it is efficient and thereafter accomodates even low light conditions.

You can use single solar cells have Voc of say > 0.6V.

Any load on VSTOR (config 2a) is unregulated, load on VOUT is regulated.

Note that you don't have to wire a load to VSTOR pin.
TI recommends putting the load on VSTOR ***when batteries are used***.
If you are using batteries, putting the load on VSTOR protects the battery from undervoltage,
since the BQ disconnects VBAT from VSTOR in that condition.

Note that the buck converter is powered from the VSTOR net.
So a load on VOUT is on the VSTOR and VBAT nets also, just with level conversion of the buck converter.
The buck converter will drain VSTOR and VBAT only to 2V anyway (to the hardwired Vbat_uv=1.95V of the BQ.)
(The BQ25505 lets you configure Vbat_uv.)
The referenced paper says that the buck converter has its own threshold (Vuvlo) which is slightly higher than Vbat_uv,
but the datasheet does not say that.

Note that the BQ dynamically switches a connection between VSTOR and VBAT, for under and over voltage conditions on VBAT.

I am now designing for configuration 2b,
with no load wired to VSTOR, all load on VOUT, regulated to 2.4V.
I don't care that it coldstarts slowly since I intend that the system coldstart very rarely,
only for very low probability, extreme weather events (many days of dark.)
The rest of the system (the MCU) will monitor voltage and just not operate so as to prevent
conditions that might require coldstart.


Configuration 4  Supercapacitor and load on VOUT pin
---

Coldstarts and charges relatively fast (the whole point of the referenced paper.)

Doesn't use the entire capacity of the supercapacitor (if the system voltage is lower than the rated Vmax voltage of the supercap.)












