---
layout: post
title: LIPO Battery Saftey and terms
author:
    name: Ethan Slattery
    email: Ethan.Slattery@yahoo.com
    github: TheSlat
date: 2016-2-11 19:05:24 -08:00
description: "LIPO battery saftey and Terms"
tags: [Battery, Safety]
---
{% include JB/setup %}
## Lithium Polymer Batteries ##
Lithium Polymer Batteries are the most common batteries you will see on R.C. vehicles since they provide the best power density, weight, and discharge/charge weight. These are all important attributes for a high performance battery. For people new to LIPO batteries this will be a basic into to what to look for and more importantly *SAFTEY*.

#### TERMS
   * **Ampere:** The SI unit of measurement for current, or the flow of electrons through an electric circuit. Since this is metric it uses metric prefixes, and is most often seen as mA, or .001 Amps. When water is used as an analogy this is similar to the flow of water in the pipes, and a large R/C motor might need 40-50 Amps peak.
   * **Amp-Hour:** Since Amps are a measure of flow rate, an Amp-Hour is a measure of capacity. A ideal 1Ah battery will allow current to flow at 1A for 1 Hour, .5A for 2 hours, 2A for 30 minutes, etc. Real world applications are much more complex of course, but it is a very useful metric. LIPO batteries can range from 500mAh (.5Ah) to 10000mAh (10Ah).
   * **Volts:** The SI unit of electrical potential, or the difference in electrical potential. In a water analogy this is similar to how forcefully the water in going through the pipes. Batteries operate at a "fixed" voltage, which is actually a nominal voltage and the actual voltage will be a little higher when fully charged and lower when running low. LIPO batteries can be of various voltages depending on the S number... 
   * **S-Number:** The S number is simply the number of cells in a LIPO battery. LIPO calls are built so that they have a nominal voltage of 3.7V, but range from 3.0V to 4.2V depending on the state of charge. Cells are connected in series which makes the voltages additive. This means a 1S battery is 3.7V, a 2S battery is 7.4V, 4S is 14.8V, etc. 
   * **C-Value:** This is somewhat specific to R/C, but is a measurement of how fast a battery can charge and/or discharge. This is very important in R/C since quick acceleration is dependant on getting large amounts of power (watts) from the battery quickly. This is expressed as a number, which you multiply by the batteries capacity to figure out the maximum rate. For example a 5000mAh 3C battery has a maximum discharge rate of 15000 mA, of 15Amps. This rating obviously need to be high enough to supply the power that the vehicle needs. If a quad-copter has 4 motors that draw 50Amps peak then the battery would need to supply at least 200Amps. If the battery is 5000mAh this is 40C! (Luckily batteries like this do exist)
   
#### SAFTEY!
LIPO batteries are known to *EXPLODE*!
![exploding battery](https://i.ytimg.com/vi/-DcpANRFrI4/hqdefault.jpg)

LIPO batteries can be very dangerous. They are the batteries responsible for all the horrible things you have seen about phones catching pillows on fire, drones exploding in airplane overhead bins, etc. Don't be scared though, these things are very rare and it is fairly easy to avoid. The biggest thing is that LIPO batteris *LOVE being at nominal voltage*. This is the easy rule of thumb to remember the main rules:
   * Never store a battery fully charged or undercharged. Store at nominal voltage
   * Never Overcharge a battery!
   * Never Over-DISHCHARGE a battery (just one more minute of flying! I swear!)
   * Never Use a physically damaged battery (cheap LIPO on ebay! never used, only dropped once!)

##### Charging
Good battery chargers will provide everything you need to follow these rules, **don't skimp on the charger**! It will have a storage setting that will charge/discharge the battery to a nice nominal voltage. It will refuse to charge a battery that is damaged or undercharged, which is good. It will have adjustable rates and smart charging. Another thing that makes LIPOs explode is charging the cells in an unbalanced way, and a good charger will prevent this.

*Don't leave a battery unatteneded while charging*. failures happen rarely, but happen fast if they do. Charge away from things that don't do well with being on fire.

##### Storage
Store batteries in a fireproof container, either a metal box or a purpose made LIPO bag. Don't buy cheap knock off's, of stuff 100000mAhs of battery into a bag rated for 5000mAh. **If a battery is damaged [follow proper disposal procedures (pdf)](http://konarcflyers.com/Lipo-Disposal.pdf)**

LIPOs hate heat, heat will degrade the lifespan of the battery dramatically. LIPOs are expensive and have pretty short lifespans as it is. if you store them in hot places and treat them rough that lifespan gets shortened a lot. LIPOs don't *hate* the cold, but they do dislike it. Cold temperatures reduce the runtime, to the point of crashing vehicles sometimes, but will not hurt the battery in general.
