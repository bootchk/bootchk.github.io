---
layout: post
title: "Fundamental configurations of BQ25570 energy harvester"
date: 2019-05-10
---


Texas Instrument's BQ25570 chip is an energy harvester.

You can configure it several ways, i.e. where do you connect the load and the storage.
(This is not about how you also configure various voltage thresholds using resistor nets.)

This is about supercap storage, not a battery.  Thats another basic configuration: what kind of storage to use.

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

The basic configurations:
   1.  SC-diode (no BQ25570 at all)
   2.  Supercapacitor on VBAT pin, load on VSTOR and VOUT
   3.  Supercapacitor and load on VOUT pin

The considerations are:
   - how fast it coldstarts and continues charging
   - how regulated the out voltages are
   - how easy is it to find appropriate solar cells
   - how well the capacitor storage is used
   - how efficient energy is harvested (MPPT)
   - what light conditions are accomodated

Configuration 1 SC-diode (no BQ25570 at all)
---

The voltage is unregulated. 
 
The solar cell can operate inefficiently (off MPP) much of the time.

A voltage drop is suffered across the blocking diode.

It constrains your choice of solar cells to those whose Voc is less than the Vmax of your system.

It charges fast, there is no coldstart of the energy harvester.

I have implemented this with an MSP430FR2433 and two IXYS KXOB22-04x3 solar cells in series generated Voc of 3.8V.


Configuration 2 Supercapacitor on VBAT pin, load on VSTOR and VOUT
---

It can coldstart very slowly, since coldstart efficiency is about 10% and the BQ clamps the solar cell to 0.3V which can be far from its MPP.

You can charge the capacitor to 5V, using its full capacity.

Once you get past coldstart, it is efficient and thereafter accomodates even low light conditions.

You can use single solar cells have Voc of say > 0.6V.

Load on VSTOR is unregulated, load on VOUT is regulated.

Note that you do not necessarily need to put any load on VSTOR.
TI recommends putting the load on VSTOR ***when batteries are used***.
If you are not using batteries, you don't need the load on VSTOR to protect the battery.

This is the configuration I am now designing for,
except that I will not put any load on VSTOR, all load on VOUT, regulated to 2.4V.
I don't care that it coldstarts slowly since I intend that it would coldstart very rarely,
only for very low probability, extreme weather events (many days of dark.)
The rest of the system (the MCU) will monitor voltage and just not operate so as to prevent
conditions that might require coldstart.


Configuration 3  Supercapacitor and load on VOUT pin
---

Coldstarts and charges relatively fast (the whole point of the referenced paper.)

Doesn't use the entire capacity of the supercapacitor (if the system voltage is lower than the rated Vmax voltage of the supercap.)












