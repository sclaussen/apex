# Neptune Apex Programming





## Notes

Subsystem Dependency Chain:
- Feed A ON -> Feed ON
- Feed B ON -> Wave ON
- Feed C ON -> Siphon ON
- Feed D ON -> Maintenance ON
- Maintenance ON => Return OFF
- Return OFF => Skimmer OFF, ATO-Enabled OFF
- Skimmer OFF => ATO-Enabled OFF
- ATO-Enabled OFF => ATO OFF



Macros:

- Feed A: Feed
  - Feed virtual outlet
  - 15 minutes
  - MP40s MP40-Feed (15% Nutrient)
  - Powerheads off
  - Lights 100%

```
Fallback OFF
Set OFF
If FeedA 000 Then ON
If Output Feed-Timer = ON Then ON
```

- Feed B: Wave
  - Wave virtual outlet
  - 15 minutes
  - MP40s MP40-Wave
  - Lights 100%

```
Fallback OFF
Set OFF
If FeedB 000 Then ON
```

- Feed C: Siphon
  - Siphon virtual outlet
  - Quasi-infinite
  - MP40s off
  - Powerheads off
  - Lights 100%

```
Fallback OFF
Set OFF
If FeedC 000 Then ON
```

- Feed D: Maintenance
  - Maintenance virtual outlet
  - Quasi-infinite
  - MP40s off
  - Powerheads off
  - Lights 100%
  - Return off
  - Skimmer off
  - ATO OFF

```
Fallback OFF
Set OFF
If FeedD 000 Then ON
```





## Return Pump

```
Fallback ON
Set ON
If Outlet Maintenance = ON Then OFF
```





## Skimmer

- Always defer start for 10 minutes to enable water levels to stabilize

```
Fallback OFF
Set ON
If Output Return = OFF Then OFF
Defer 010:00 Then ON
```





## Auto Top Off System



### Probes

- ATO-LO: Sump water level low probe
- ATO-HI: Sump water level high probe
- ATO-WL: ATO container water level



### ATO-LO-Alarm

Enables an alarm if the water level has been below the ATO-LO probe
for too long.

```
Fallback OFF
Set OFF
If ATO-LO OPEN Then ON
Defer 090:00 Then ON
```



### ATO-HI-Alarm

Enables an alarm if the water level has been above the ATO-HI probe
for too long.

```
Set OFF
If ATO-HI CLOSED Then ON
Defer 090:00 Then ON
```



### ATO-Enabled

```
Fallback ON
Set ON
If Output Return = OFF Then OFF
If Output Skimmer = OFF Then OFF
Defer 005:00 Then ON
```



### ATO

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





## MP40QDs

### MP40-L

```
Fallback MP40-Feed
tdata 00:00:00,0,0,10,0,0,0,0,0,0,0,0,0,0
tdata 05:00:00,0,0,15,2,0,0,0,0,0,0,0,0,0
tdata 07:00:00,0,0,20,7,0,0,0,0,0,0,0,0,0
tdata 08:45:00,0,0,80,1,0,0,0,0,0,0,0,0,0
tdata 10:15:00,0,0,60,7,0,0,0,0,0,0,0,0,0
tdata 11:45:00,0,0,100,8,0,0,0,0,0,0,0,0,0
tdata 13:15:00,0,0,80,1,0,0,0,0,0,0,0,0,0
tdata 14:30:00,0,0,100,8,0,0,0,0,0,0,0,0,0
tdata 16:20:00,0,0,60,7,0,0,0,0,0,0,0,0,0
tdata 18:00:00,0,0,100,3,200,0,0,0,0,0,0,0,0
tdata 19:29:00,0,0,68,1,0,0,0,0,0,0,0,0,0
tdata 20:00:00,0,0,30,7,0,0,0,0,0,0,0,0,0
tdata 21:00:00,0,0,15,2,0,0,0,0,0,0,0,0,0
tdata 23:59:00,0,0,10,0,0,0,0,0,0,0,0,0,0
If Output Feed = ON Then MP40-Feed
If Output Wave = ON Then MP40-Wave
If Output Siphon = ON Then OFF
If Output Maintenance = ON Then OFF
```



### MP40-R

```
Fallback MP40-Feed
tdata 00:00:00,0,0,10,0,0,0,0,0,0,0,0,0,0
tdata 05:00:00,0,0,15,2,0,0,0,0,0,0,0,0,0
tdata 07:00:00,0,0,20,7,0,0,0,0,0,0,0,0,0
tdata 08:45:00,0,0,80,1,0,0,0,0,0,0,0,0,0
tdata 10:15:00,0,0,60,7,0,0,0,0,0,0,0,0,0
tdata 11:45:00,0,0,100,8,0,0,0,0,0,0,0,0,0
tdata 13:15:00,0,0,80,1,0,0,0,0,0,0,0,0,0
tdata 14:30:00,0,0,100,8,0,0,0,0,0,0,0,0,0
tdata 16:20:00,0,0,60,7,0,0,0,0,0,0,0,0,0
tdata 18:00:00,0,0,100,3,200,0,0,0,0,0,0,0,0
tdata 19:29:00,0,0,68,1,0,0,0,0,0,0,0,0,0
tdata 20:00:00,0,0,30,7,0,0,0,0,0,0,0,0,0
tdata 21:00:00,0,0,15,2,0,0,0,0,0,0,0,0,0
tdata 23:59:00,0,0,10,0,0,0,0,0,0,0,0,0,0
If Output Feed = ON Then MP40-Feed
If Output Wave = ON Then MP40-Wave
If Output Siphon = ON Then OFF
If Output Maintenance = ON Then OFF
```




## Powerheads

```
Fallback ON
Set OFF
If Time 08:00 to 22:00 Then ON
If Outlet Feed = ON Then OFF
If Outlet Siphon = ON Then OFF
If Outlet Maintenance = ON Then OFF
```





## Ecotech Radion XR30w G4 LED Lights

I have two schemes for the Radion lights:

1. FOWLR (Type Fish Only with Live Rock): One big hill, AB+ like

2. Mixed Coral Lab AB+ (Type Mixed): Standard AB+ profile, ramp up for
   an hour, 100% intensity for 7 hours of blue lights on full and the
   remainder at 24%, then ramps down for an hour.  The only adjustment
   I've made is blue light on the ramp down and a 1% night light until
   late.



### Radion-L

```
Fallback OFF
tdata 00:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 09:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 09:45:00,0,0,30,0,0,100,100,100,0,0,100,0,0
tdata 13:00:00,0,0,75,24,24,100,100,100,24,24,100,0,0
tdata 18:00:00,0,0,60,24,24,100,100,100,24,24,100,0,0
tdata 19:45:00,0,0,30,0,0,100,100,100,0,0,100,0,0
tdata 20:30:00,0,0,1,0,0,100,100,100,0,0,100,0,0
tdata 23:59:00,0,0,0,0,0,0,0,0,0,0,0,0,0
If Output Feed = ON Then 100
If Output Wave = ON Then 100
If Output Siphon = ON Then 100
If Output Maintenance = ON Then 100
```



### Radion-R

```
Fallback OFF
tdata 00:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 09:00:00,0,0,0,0,0,0,0,0,0,0,0,0,0
tdata 09:45:00,0,0,30,0,0,100,100,100,0,0,100,0,0
tdata 13:00:00,0,0,75,24,24,100,100,100,24,24,100,0,0
tdata 18:00:00,0,0,60,24,24,100,100,100,24,24,100,0,0
tdata 19:45:00,0,0,30,0,0,100,100,100,0,0,100,0,0
tdata 20:30:00,0,0,1,0,0,100,100,100,0,0,100,0,0
tdata 23:59:00,0,0,0,0,0,0,0,0,0,0,0,0,0
If Output Feed = ON Then 100
If Output Wave = ON Then 100
If Output Siphon = ON Then 100
If Output Maintenance = ON Then 100
```





## Auto Water Exchange System

There are 3 "sets" of schemes saved publicly for AWE handling 10%,
15%, and 20% auto water exchanges respectively.  Here's the
calculations:
- Total tank/sump volume (minus live rock et al) is 125G
- 10% change: 12.5G/W, 1.79G/D, 6.76L/D, 677ML/D
- 15% change: 18.75G/W, 2.68G/D, 10.14L/D, 1014ML/D
- 20% change: 25G/W, 3.58G/D, 13.52L/D, 1352ML/D



### AWE-Old

```
Fallback OFF
tdata 00:00:00,1,5,0,78,2,148,130,0,78,2,28,11,0
Fallback ON
Set ON
If Output Maintenance = ON Then OFF
```



### AWE-New

```
Fallback OFF
tdata 00:00:00,1,21,0,78,2,148,130,0,78,2,28,11,0
Fallback ON
Set ON
If Output Maintenance = ON Then OFF
```





## Feed

### Feed-Timer

```
Fallback OFF
Set OFF
If Time 10:00 to 10:10 Then ON
If Time 14:00 to 14:10 Then ON
If Time 20:00 to 20:10 Then ON
```



### Feed-AFS

```
Fallback OFF
Set OFF
If Output Feed-Timer = ON Then ON
Defer 000:30 Then ON
```




## Heater1

```
77.9 - 78.1
```





## Heater2

```
78.0 - 78.1
```





## Sump Light

- Turn on the outlet for the LED over the sump.  If the outlet is on,
  the LED is then turned on/off using a physical switch on the LED
  itself.

```
Fallback OFF
Set OFF
If Time 08:00 to 22:00 Then ON
```





## System Alarms



### Alarm-Error

```
Fallback OFF
Set OFF
If Error ATO Then ON
If Error Return Then ON
```



### Alarm-Warn

```
Fallback OFF
Set OFF
```



### Alarm-SMS

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
