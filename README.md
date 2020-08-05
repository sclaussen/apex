# System Overview

## Equipment summary

- Display Tank: **Elos 120XL Tank (127G gross/103G net)**
- Display Tank Lights: **Ecotech Radion XR30w G4 x2**
- Display Tank Primary Powerheads: **Ecotech MP40QD Powerhead x2**
- Display Tank Secondary Powerheads: **Hydor Koralia Third Generation Powerhead (3450gph) x2**
- Display Tank Secondary Powerheads: **Hydor Koralia Third Generation Powerhead (1350gph) x2**
- RODI: **7 Stage Pro Plus 75 GPD RO/DI System**
- RODI Pressure Boost Pump: **Aquatec CDP 8800**
- Water Storage Container (ATO): **40G Ace Roto Mold vt00040swss**
- Water Storage Container (NSW): **40G Ace Roto Mold vt00040swss**
- Water Storage Container (Reserve): **80G Ace Roto Mold**
- Water Storage Pump: **BlueLine Aqua Pump 70 HD**
- Water Storage Water Level Sensor: **Neptune Optical Sensor**
- Sump: **Custom Acrylic Sump (35G)**
- Controller: **Neptune Apex 2016 Controller**
- Return Pump: **Neptune COR-15 Return Pump**
- Skimmer: **Regal 200INT Skimmer**
- Skimmer Pump: **VarioS-4 S Pinwheel Pump**
- Refugium: **Chaeto Refugium 17G (14%)**
- Refugium Light: **Kessil H380 Halo II LED Algae Grow Light**
- Reactor: **BRS Single Deluxe Media Reactor**
- Reactor Pump: **Maxi-Jet 1200**
- Kalk Stirrer: **Avast Marine K2 Kalk Stirrer**
- Kwalk Doser: **Neptune DOS**
- Heaters: **Eheim Jager 300W Heater x2**
- Auto Top Off: **Neptune ATK Auto Top Off**
- Auto Water Exchange: **Neptune DOS**
- Feeder: **Neptune AFS**
- PAR Meter: **Neptune ASM Module**
- Ecotech Radion and MP40 controller: **Neptune WXM Module**



## System Diagram

![System Overview](./system.jpg)





# Neptune Apex Programming





## Feed A, B, C, and D

In summary I've programmed the four Feed macros on the Apex as follows:
- Feed A: Feed mode: Mysis, et al
- Feed B: Wave mode: Demo mode
- Feed C: Siphon mode: Siphon out the display tank
- Feed D: Maintenance mode: Most subystems are off



### Feed A: Feed

- Note: The Feed-Timer is used for auto-feeding pellets via the
  Neptune AFS.  The Feed A macro is used for feeding the fish
  manually with mysis, et al.  The auto feeding mechanism is a super
  set of the manually feeding mechanism.
- Utilizes the **Feed** and **FdA-Feed** virtual outputs and the **FdA-Feed** Profile
- 15 minutes
- MP40s FdA-Feed (10% Nutrient)
- Powerheads off
- Radions 100%
- Dependency chain (for Feed A or Feed-Timer)
  - Feed-Timer ON -> Feed ON
  - Feed A ON -> Feed ON
  - Feed ON -> MP40 FdA-Feed, Powerheads OFF, Radions 100%
  - Feed-Timer ON -> Feed-AFS ON

**FdA-Feed**
```
Fallback OFF
Set OFF
If FeedA 000 Then ON
If Output FdC-Siphon = ON Then OFF
If Output FdD-Maint = ON Then OFF
```



### Feed B: Wave

- Utilizes the **100-Pulse** virtual output and the **100-Pulse** Profile
- 15 minutes
- MP40s 100-Pulse
- Radions 100%
- Dependency chain
  - Feed B ON -> 100-Pulse ON
  - 100-Pulse ON -> MP40 100-Pulse, Radion 100%

**FdB-Wave**
```
Fallback OFF
Set OFF
If FeedB 000 Then ON
If Output FdC-Siphon = ON Then OFF
If Output FdD-Maint = ON Then OFF
```


### Feed C: Siphon

- Utilizes the **Siphon** virtual output
- Quasi-infinite
- MP40s off
- Powerheads off
- Radions 100%
- Dependency chain
  - Feed C ON -> Siphon ON
  - Siphon ON -> MP40 OFF, Powerheads OFF, Radions 100%

**FdC-Siphon**
```
Fallback OFF
Set OFF
If FeedC 000 Then ON
If Output FdD-Maint = ON Then OFF
```



### Feed D: Maintenance

- Utilizes the **Maintenance** virtual output
- Quasi-infinite
- MP40s off
- Powerheads off
- Radions 100%
- Return off
- Skimmer off
- ATO OFF
- AWE OFF
- Kalk OFF
- Feed OFF
- Feed-Timer OFF
- Dependency chain:
  - Feed D ON -> Maintenance ON
  - Maintenance ON -> Return OFF, MP40s OFF, Powerheads OFF, Radions OFF, AWE OFF, Kalk OFF
  - Return OFF -> Skimmer OFF, ATO-Enabled OFF
  - Skimmer OFF -> ATO-Enabled OFF
  - ATO-Enabled OFF -> ATO OFF

**FdD-Maint**
```
Fallback OFF
Set OFF
If FeedD 000 Then ON
```





## Return Pump

Type:
- Neptune COR-15 Return Pump

**Return** Output
```
Fallback ON
Set ON
If Output FdC-Maint = ON Then OFF
```





## Skimmer

Type:
- Regal 200INT Skimmer
- VarioS-4 S Pinwheel Pump

Always defer the start to enable the water levels to stabilize.

**Skimmer** Output
```
Fallback OFF
Set ON
If Output Return = OFF Then OFF
Defer 005:00 Then ON
```





## Auto Top Off System

Type:
- Neptune ATK Auto Top Off
- Neptune Optical Sensor

### Probes

- **ATO-LO**: Sump low water probe
- **ATO-HI**: Sump high water probe
- **ATO-WL**: ATO container low water probe



### ATO-LO-Alarm

Enables an alarm if the water level has been below the ATO-LO probe
for too long.

**ATO-LO-Alarm** Virtual Output
```
Fallback OFF
Set OFF
If ATO-LO OPEN Then ON
Defer 090:00 Then ON
```



### ATO-HI-Alarm

Enables an alarm if the water level has been above the ATO-HI probe
for too long.

**ATO-HI-Alarm** Virtual Output
```
Set OFF
If ATO-HI CLOSED Then ON
Defer 090:00 Then ON
```



### ATO-Enabled

**ATO-Enabled** Virtual Output
```
Fallback ON
Set ON
If Output Return = OFF Then OFF
If Output Skimmer = OFF Then OFF
Defer 005:00 Then ON
```



### ATO

**ATO** Output
```
Fallback OFF
Set OFF
If ATO-LO OPEN Then ON
If ATO-HI CLOSED Then OFF
If ATO-WL OPEN Then OFF
If Output ATO-Enabled = OFF Then OFF
When ON > 005:00 Then OFF
Defer 000:10 Then ON
Defer 000:04 Then OFF
Min Time 060:00 Then OFF
```





## Primary Powerheads

Type:
- Ecotech MP40QD Powerhead x2

### MP40--L

**MP40--L** Output
```
Fallback PSlowest
tdata 00:00:00,0,0,10,2,0,0,0,0,0,0,0,0,0
tdata 07:00:00,0,0,10,7,0,0,0,0,0,0,0,0,0
tdata 08:46:00,0,0,100,1,0,0,0,0,0,0,0,0,0
tdata 10:25:00,0,0,85,3,150,0,0,0,0,0,0,0,0
tdata 11:45:00,0,0,100,8,0,0,0,0,0,0,0,0,0
tdata 13:16:00,0,0,90,1,0,0,0,0,0,0,0,0,0
tdata 14:30:00,0,0,100,8,0,0,0,0,0,0,0,0,0
tdata 16:23:00,0,0,85,1,0,0,0,0,0,0,0,0,0
tdata 18:00:00,0,0,100,3,200,0,0,0,0,0,0,0,0
tdata 19:29:00,0,0,68,1,0,0,0,0,0,0,0,0,0
tdata 20:00:00,0,0,30,7,0,0,0,0,0,0,0,0,0
tdata 21:00:00,0,0,10,2,0,0,0,0,0,0,0,0,0
tdata 23:59:00,0,0,10,2,0,0,0,0,0,0,0,0,0
If Output FdA-Feed = ON Then PFeed
If Output FdB-Wave = ON Then PWave
If Output FdC-Siphon = ON Then PSiphon
If Output FdD-Maint = ON Then OFF
If Output MP40-Const = ON Then PConst
If Output MP40-Const01 = ON Then PConst01
If Output MP40-Lagoon = ON Then PLagoon
If Output MP40-Nutr = ON Then PNutr
If Output MP40-PulseP5 = ON Then PPulseP5
If Output MP40-PulseP8 = ON Then PPulseP8
If Output MP40-Pulse2 = ON Then PPulse2
If Output MP40-Pulse3 = ON Then PPulse3
If Output MP40-Pulse5 = ON Then PPulse5
If Output MP40-Pulse8 = ON Then PPulse8
If Output MP40-Pulse10 = ON Then PPulse10
If Output MP40-ReefCr = ON Then PReefCr
If Output MP40-Slowest = ON Then PSlowest
If Output MP40-Tidal = ON Then PTidal
If Time 21:00 to 07:00 Then PSleep
```



### MP40--R

**MP40--R** Output
```
Fallback PSlowest
tdata 00:00:00,0,0,10,2,0,0,0,0,0,0,0,0,0
tdata 07:00:00,0,0,10,7,0,0,0,0,0,0,0,0,0
tdata 08:46:00,0,0,100,1,0,0,0,0,0,0,0,0,0
tdata 10:25:00,0,0,85,3,150,0,0,0,0,0,0,0,0
tdata 11:45:00,0,0,100,8,0,0,0,0,0,0,0,0,0
tdata 13:16:00,0,0,90,1,0,0,0,0,0,0,0,0,0
tdata 14:30:00,0,0,100,8,0,0,0,0,0,0,0,0,0
tdata 16:23:00,0,0,85,1,0,0,0,0,0,0,0,0,0
tdata 18:00:00,0,0,100,3,200,0,0,0,0,0,0,0,0
tdata 19:29:00,0,0,68,1,0,0,0,0,0,0,0,0,0
tdata 20:00:00,0,0,30,7,0,0,0,0,0,0,0,0,0
tdata 21:00:00,0,0,10,2,0,0,0,0,0,0,0,0,0
tdata 23:59:00,0,0,10,2,0,0,0,0,0,0,0,0,0
If Output FdA-Feed = ON Then PFeed
If Output FdB-Wave = ON Then PWave
If Output FdC-Siphon = ON Then PSiphon
If Output FdD-Maint = ON Then OFF
If Output MP40-Const = ON Then PConst
If Output MP40-Const01 = ON Then PConst01
If Output MP40-Lagoon = ON Then PLagoon
If Output MP40-Nutr = ON Then PNutr
If Output MP40-Pulse05 = ON Then PPulse05
If Output MP40-Pulse08 = ON Then PPulse08
If Output MP40-Pulse2 = ON Then PAntiSync
If Output MP40-Pulse3 = ON Then PAntiSync
If Output MP40-Pulse5 = ON Then PAntiSync
If Output MP40-Pulse10 = ON Then PAntiSync
If Output MP40-ReefCr = ON Then PReefCr
If Output MP40-Slowest = ON Then PSlowest
If Output MP40-Tidal = ON Then PTidal
If Time 21:00 to 07:00 Then PSleep
```





## Secondary Powerheads

Type:
- ? x4

**Powerheads** Output
```
Fallback ON
Set OFF
If Time 08:00 to 22:00 Then ON
If Output Feed = ON Then OFF
If Output FdC-Siphon = ON Then OFF
If Output FdC-Maint = ON Then OFF
```





## Lights

Type:
- Ecotech Radion XR30w G4 LED x2

Strict AB+ from 12:00pm to 8pm, 1 hour ramp up and down from 0% to
100% intensity, 7 hours at maximum intensity.
- 24%: Red, Green, Cool White, Warm White
- 100%: Blue, Royal Blue, Ultra Violet, Violet


### Radion--L

FOWLR

**Radion--L** Output
```
Fallback OFF
tdata 00:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 09:00:00,0,0,1,0,0,0,0,0,0,0,0,0,0
tdata 09:45:00,0,0,30,0,0,100,100,100,0,0,100,0,0
tdata 13:00:00,0,0,60,24,24,100,100,100,24,24,100,0,0
tdata 18:00:00,0,0,45,24,24,100,100,100,24,24,100,0,0
tdata 19:45:00,0,0,30,0,0,100,100,100,0,0,100,0,0
tdata 20:30:00,0,0,1,0,0,100,100,100,0,0,100,0,0
tdata 23:59:00,0,0,1,0,0,0,0,0,0,0,0,0,0
If Output Feed = ON Then 100
If Output FdC-Siphon = ON Then 100
If Output FdD-Maint = ON Then 100
```

AB+

**Radion--L** Output
```
Fallback OFF
tdata 00:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 12:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 13:00:00,0,0,100,24,24,100,100,100,24,24,100,0,0
tdata 20:00:00,0,0,100,24,24,100,100,100,24,24,100,0,0
tdata 21:00:00,0,0,2,24,24,100,100,100,24,24,100,0,0
tdata 22:00:00,0,0,1,24,24,100,100,100,24,24,100,0,0
tdata 23:59:00,0,0,0,0,0,0,0,0,0,0,0,0,0
If Output Feed = ON Then 100
If Output FdC-Siphon = ON Then 100
If Output FdD-Maint = ON Then 100
```



### Radion--R

FOWLR

**Radion--R** Output
```
Fallback OFF
tdata 00:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 09:00:00,0,0,1,0,0,0,0,0,0,0,0,0,0
tdata 09:45:00,0,0,30,0,0,100,100,100,0,0,100,0,0
tdata 13:00:00,0,0,60,24,24,100,100,100,24,24,100,0,0
tdata 18:00:00,0,0,45,24,24,100,100,100,24,24,100,0,0
tdata 19:45:00,0,0,30,0,0,100,100,100,0,0,100,0,0
tdata 20:30:00,0,0,1,0,0,100,100,100,0,0,100,0,0
tdata 23:59:00,0,0,1,0,0,0,0,0,0,0,0,0,0
If Output Feed = ON Then 100
If Output FdC-Siphon = ON Then 100
If Output FdD-Maint = ON Then 100
```

AB+

**Radion--R** Output
```
Fallback OFF
tdata 00:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 12:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 13:00:00,0,0,100,24,24,100,100,100,24,24,100,0,0
tdata 20:00:00,0,0,100,24,24,100,100,100,24,24,100,0,0
tdata 21:00:00,0,0,2,24,24,100,100,100,24,24,100,0,0
tdata 22:00:00,0,0,1,24,24,100,100,100,24,24,100,0,0
tdata 23:59:00,0,0,0,0,0,0,0,0,0,0,0,0,0
If Output Feed = ON Then 100
If Output FdC-Siphon = ON Then 100
If Output FdD-Maint = ON Then 100
```





## Auto Water Exchange System

Type:
- Neptune DOS

There are 3 "sets" of schemes saved publicly for AWE handling 10%,
15%, and 20% auto water exchanges respectively.  Here's the
calculations:
- Total tank/sump volume (minus live rock et al) is 125G
- 10% change: 12.5G/W, 1.79G/D, 6.76L/D, 677ML/D
- 15% change: 18.75G/W, 2.68G/D, 10.14L/D, 1014ML/D
- 20% change: 25G/W, 3.58G/D, 13.52L/D, 1352ML/D



### AWE-Old

**AWE-Old** Output
```
Fallback OFF
tdata 00:00:00,1,5,0,78,2,148,130,0,78,2,28,11,0
Fallback ON
Set ON
If Output FdD-Maint = ON Then OFF
```



### AWE-New

**AWE-New** Output
```
Fallback OFF
tdata 00:00:00,1,21,0,78,2,148,130,0,78,2,28,11,0
Fallback ON
Set ON
If Output FdD-Maint = ON Then OFF
```





## Feed

Type:
- Neptune AFS

### **Feed** Virtual Output
```
Fallback OFF
Set OFF
If Output Feed-Timer = ON Then ON
If Output FdA-Feed = ON Then ON
If Output FdC-Siphon = ON Then OFF
If Output FdD-Maint = ON Then OFF
```

### **Feed-Timer** Virtual Output

```
Fallback OFF
Set OFF
If Time 10:00 to 10:10 Then ON
If Time 14:00 to 14:10 Then ON
If Output FdC-Maint = ON Then OFF
```



### Feed-AFS

**Feed-AFS** Virtual Output
```
Fallback OFF
Set OFF
If Output Feed-Timer = ON Then ON
Defer 000:30 Then ON
```




## Refugium Light

Type:
- Kessil H380 Halo II LED Algae Grow Light

**Refugium** Output
```
Fallback OFF
Set OFF
If Time 21:00 to 24:00 Then ON
```





## Reactor

**Reactor** Output
```
Fallback OFF
Set ON
If Output Return = OFF Then OFF
Defer 005:00 Then ON
```





## Kalkwasser

Type:
- Avast Marine K2 Kalk Stirrer
- Neptune DOS

### Kalk-Stirrer

**Kalk-Stirrer** Output
```
Fallback ON
Set ON
If Output FdC-Maint = ON Then OFF
```



### Kalk DOS

**Kalk-DOS** Output
```
tdata tbd
Fallback ON
Set ON
If Output FdD-Maint = ON Then OFF
```





## Heater1

Type:
- Eheim Jager 300W Heater x2

**Heater1** Output
```
77.9 - 78.1
```





## Heater2

Type:
- Eheim Jager 300W Heater x2

**Heater2** Output
```
78.0 - 78.1
```





## Sump Light

Turn on the output for the LED over the sump.  If the output is on,
the LED is then turned on/off using a physical switch on the LED
itself.

**Sump-Light** Output
```
Fallback OFF
Set OFF
If Time 08:00 to 22:00 Then ON
```





## System Alarms



### Apex-Warn

**Apex-Warn** Output
```
Fallback OFF
Set OFF
```



### Apex-Alarm

**Apex-Alarm** Output
```
Fallback OFF
Set OFF
If Error ATO Then ON
If Error Return Then ON
```



### Apex-SMS

**Apex-SMS** Output
```
Fallback OFF
Set OFF
If Error ATO Then ON
If Error Return Then ON
If pH > 8.60 Then ON
If pH < 8.00 Then ON
If Tmp > 82.0 Then ON
If Tmp < 75.0 Then ON
If ORP < 250 Then ON
If ORP > 450 Then ON
If ATO-WL OPEN Then ON
If Output ATO-LO-Alarm = ON Then ON
If Output ATO-HI-Alarm = ON Then ON
```





## Booster Pump

Type:
- Aquatec CDP 8800

The Aquatec CDP 8800 booster pump increases the water pressure going
into my RO/DI system ensuring that I can product ~75GPD.

**Booster-Pump** Output
```
Fallback ON
Set ON
```





## MP40 Profiles and Virtual Outputs

I've created a handful of different profiles to run the MP40s in
different modes.



### **PConst** Profile: Constant mode at 100%

Activation:
- Activated when MP40-Const is ON

**MP40-Const** Virtual Output
```
Fallback OFF
Set OFF
```



### **PConst01** Profile: Constant mode at 1%

Activation:
- Activated when MP40-Const01 is ON

**MP40-Const01** Virtual Output
```
Fallback OFF
Set OFF
```



### **PFeed** Profile: Const mode at 1%

Activation:
- Activated when FdA-Feed is ON

**FdA-Feed** Virtual Output
```
Fallback OFF
Set OFF
```



### **PLagoon** Profile: Lagoon mode at 100%

Activation:
- Activated when MP40-Lagoon is ON

**MP40-Lagoon** Virtual Output
```
Fallback OFF
Set OFF
```



### **PNutr** Profile: Nutrient mode at 100%

Activation:
- Activated when MP40-Nutr is ON

**MP40-Nutr** Virtual Output
```
Fallback OFF
Set OFF
```



### **PPulse2** Profile: Pulse mode at 100% 2s

Activation:
- Activated when MP40-Pulse2 is ON

**MP40-Pulse2** Virtual Output
```
Fallback OFF
Set OFF
If FeedB 000 Then ON
```



### **PPulse3** Profile: Pulse mode at 100% 3s

Activation:
- Activated when MP40-Pulse3 is ON

**MP40-Pulse3** Virtual Output
```
Fallback OFF
Set OFF
If FeedB 000 Then ON
```



### **PReefCr** Profile: ReefCrest mode at 100%

Activation:
- Activated when MP40-ReefCr is ON

**MP40-ReefCr** Virtual Output
```
Fallback OFF
Set OFF
```



### **PSiphon** Profile: Constant mode at 1%

Activation:
- Activated when FdC-Siphon is ON

**FdC-Siphon** Virtual Output
```
Fallback OFF
Set OFF
```



### **PSlowest** Profile: Constant mode at 1%

Activation:
- Activated when MP40-Slowest is ON

**MP40-Slowest** Virtual Output
```
Fallback OFF
Set OFF
```



### **PTidal**: Tidal mode at 100%

Activation:
- Activated when MP40-Tidal is ON

**MP40-Tidal** Virtual Output
```
Fallback OFF
Set OFF
```



### **PWave** Profile: Pulse mode at 100% 0.8s

Activation:
- Activated when FdB-Wave is ON

**FdB-Wave** Virtual Output
```
Fallback OFF
Set OFF
```
