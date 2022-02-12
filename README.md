
[![Arduino CI](https://github.com/RobTillaart/M62429/workflows/Arduino%20CI/badge.svg)](https://github.com/marketplace/actions/arduino_ci)
[![Arduino-lint](https://github.com/RobTillaart/M62429/actions/workflows/arduino-lint.yml/badge.svg)](https://github.com/RobTillaart/M62429/actions/workflows/arduino-lint.yml)
[![JSON check](https://github.com/RobTillaart/M62429/actions/workflows/jsoncheck.yml/badge.svg)](https://github.com/RobTillaart/M62429/actions/workflows/jsoncheck.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/RobTillaart/M62429/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/release/RobTillaart/M62429.svg?maxAge=3600)](https://github.com/RobTillaart/M62429/releases)


# M62429

Arduino library for M62429 volume control IC.


## Description

This library is used to set the attenuation (volume) of the 
M62429 IC a.k.a. FM62429.

The communication needs a minimum delay of 1.6 microseconds. 
This is defined in the library in **M62429_CLOCK_DELAY** == 2
For AVR (UNO, slow device) it is defined as 0, as the digitalWrite
takes time enough. 

For faster processors this define can be overruled runtime by setting it 
before including "M62429.h" or by defining it as command line parameter.


## Interface

The interface is straightforward

- **void begin(uint8_t dataPin, uint8_t clockPin)** defines the clock and data pin.
One has to create one object per IC. 
- **int getVolume(uint8_t channel)** channel is 0 or 1 or 2 (both). In the latter
case the volume of channel 0 is used as volume of both channels.
- **int setVolume(uint8_t channel, uint8_t volume)** 
channel = { 0, 1, 2 = both; volume = {0 .. 255 }
- **void incr()** increment volume of both channels until max is reached.
This is another way to set volume that is better suited for a rotary 
encoder or a \[+\] button
- **void decr()** decrement volume of both channels until 0 is reached. See **incr()**.
- **void average()** averages the 2 channels to same = average level.  
Sort of set balance in the middle functionality.
- **void muteOn()** silences both channels but remembers the volume..
GetVolume() will return the 'saved' volume value.
- **void muteOff()** resets the volume per channel again.
- **bool isMuted()** returns the muted state. 

Note: the volume goes from 0..255 while the actual steps go from 0..87.
Therrefor not every step in volume will make a "real" step (roughly 1 in 3).


## Operation

See examples


## Future

#### Mixer

- Control multiple M62429 IC's 
- One shared dataPin and clockPin per IC. 
- use a PCF8574 / PCF8575 as selector 
- would allow for 16 stereo or 32 mono channels. 
Runtime configuration mono / stereo would be cool.

```
  PCF8574 or PCF8575 is used as device selector
  AND port takes DATA and CLOCK to right M62429
  identical schema for all 8/16 ports

             PCF8575                 AND             M62429
           +---------+              +---+          +---------+
           |         |=======+======| & |==========| data    |
           |         |   +===|======|   |          |         |
   - I2C - |         |   |   |      +---+          |         |
           |         |   |   |                     |         |
           |         |   |   |      +---+          |         |
           |         |   |   +======| & |==========| clock   |
           |         |   |     +====|   |          |         |
           |         |   |     |    +---+          |         |
           +---------+   |     |                   +---------+
                         |     |
     DATA  ==============+     |
     CLOCK ====================+
```


#### Other

- model with one **volume(0..100%)** and one **balance(-100..100)** == **pan()**.  
Also a **left()** and **right()** incremental balance might be added.
This could work better than 2 separate volume channels.
- change **getVolume(both)** to return max of the two channels?
- **Mute()** could be per channel, default = both / all.
- **mute50()** reduces levels with 50% (rounded down?).
- optimize when volume is already set? 
  - e.g. average function.
  - muteOff will fail ? investigate

**wont**
- **muteOff()** should increase gradually.  takes too much blocking time.


