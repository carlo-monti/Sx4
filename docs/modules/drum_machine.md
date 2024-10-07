## drum_machine

Drum machine is a sample based 4 track drum machine. 

### Tracks
Each track can follow the internal tempo or the global tempo of the drum machine. Pay attention: when you select sync mode on a track, the
sync is  the global sync of the drum machine, not the global sync of the song!

### Fx send
The module has 8 audio outputs. The second 4-ple is intended to be an "aux/fx send".

Each track output can be spatialized using the automation. This will route/spread the track sound to the 4 output (FR, FL, RR, RL). The fx send
slider allow the audio of each track to be sent to the aux/fx outputs.

### Fx chain

The fx chain is inspired to the Kaoss Pad. You can select a preset that is a combination of two different effects (one for the x and another for the y axis).
It allow to select which tracks are affected. 

### Samples

The samples must be positioned in the `dm_samples` in a folder named sequentially from `0` up.
The samples inside a folder must be named sequentially from `0` to up (`dm_samples/0/0.wav`, `dm_samples/0/1.wav`, ...).
The samples should be organized in banks of 16 in an "Ableton" style:
| Attempt | #1  | #2  |
| ------- | --- | --- |
| Seconds | 301 | 283 |



|-----|------------------------|
| 0   | kick            |
| 1   | rim            |
| 2   | ch           |
| 3   | chet         |
| 4   | dead      |
| 5   | dono             |
| 6   | delhain            |
| 7   | ff       |
| 8   | delime             |
| 9   | delsion        |
| 10  | delback         |
| 11  | deilter           |
| 12  | delay/wet          |
| 13  | grxture           |
| 14  | ode              |
| 15  | gronstant          |
| 16  | grnssition          |

### OSC implementation

Drum machine has global parameters (identified by the number 0) and submodule parameters (identified by numbers 1,2,3,4).

#### Global
Here is the implementation for the global parameters of the drum machine. To send a OSC global command use the number 0.
I.e. to send global sync value, send a message to `dm_osc_name/0/1`.

Presets
|-----|--------------------------|---|
| 0   | volume global                |   |
| 1   | global sync            |   |
| 2   | global division            |   |
| 3   | global bpm          |   |
| 4   | fx chain effect                |   |
| 5   | fx chain select track 1           |   |
| 6   | fx chain select track 2             |   |
| 7   | fx chain select track 3           |   |
| 8   | fx chain select track 4           |   |

#### Submodules
Drum machine has 4 independent submodules and below here is the OSC implementation for them. To send a OSC command prepend the sub-module id (1,2,3,4).
I.e. to send chance value to submodule 2 send a message to `dm_osc_name/2/6`.

Presets (ranges are 0-127 except where indicated)
|-----|--------------------------|---|
| 1   | set kit                  |   |
| 2   | n of steps               |   |
| 3   | volume of track            |   |
| 4   | volume random            |   |
| 5   | chance                   |   |
| 6   | microtiming              |   |
| 7   | automation sync          |   |
| 8   | automation division      |   |
| 9   | automation bpm           |   |
| 10  | automation shape         |   |
| 11  | automation transition    |   |
| 12  | automation spread        |   |
| 13  | automation phase         |   |
| 14  | racket division          |   |
| 15  | racket random            |   |
| 16  | envelope                 |   |
| 17  | reverse                  |   |
| 18  | pitch                    |   |
| 19  | random pitch             |   |
| 20  | micro/wide pitch         |   |
| 21  | mute                     |   |
| 22  | bpm                      |   |
| 23  | hit length               |   |
| 24  | sync                     |   |
| 25  | division                 |   |
| 26  | wavefold amount          |   |
| 27  | wavefold select          |   |
| 28  | filter select            |   |
| 29  | filter frequency         |   |
| 30  | filter q                 |   |
| 31  | rngs model               |   |
| 32  | rngs damping             |   |
| 33  | rngs brightness          |   |
| 34  | rngs structure           |   |
| 35  | rngs position            |   |
| 36  | rngs transposition       |   |
| 37  | lfo1 freq                |   |
| 38  | lfo1 q                   |   |
| 39  | lfo1 waveform            |   |
| 40  | lfo1 multiplier          |   |
| 41  | lfo2 freq                |   |
| 42  | lfo2 q                   |   |
| 43  | lfo2 waveform            |   |
| 44  | lfo2 multiplier          |   |
| 45  | fx chain select fx       |   |
| 46  | effect send              |   |
| 47  | lfo1 assign              |   |
| 48  | lfo2 assign              |   |
| 49  | density                  |   |
| 50  | type of fill             |   |
| 51  | racketing division       |   |
| 52  | racketing random         |   |
| 53  | rngs dry/wet             |   |
| 54  | rngs pitch               |   |
| 55  | rngs poly                |   |
| 56  | rngs exciter onoff       |   |
| 57  | wavefold mix             |   |
| 58  | lfo1 sync                |   |
| 59  | lfo1 phase               |   |
| 60  | lfo2 sync                |   |
| 61  | lfo2 phase               |   |
| 62  | automation trigger onoff |   |

Control
|-----|--------------------------|---|
| 100 | write to sequence        |   |
| 101 | sample +                 |   |
| 102 | sample -                 |   |
| 103 | folder +                 |   |
| 104 | folder -                 |   |
| 105 | reset                    |   |
| 106 | audition envelope        |   |
| 107 | fx chain x               |   |
| 108 | fx chain y               |   |
| 109 | fx chain activation      |   |
| 110 | automation shape +       |   |
| 111 | automation shape -       |   |
| 112 | rngs trigger             |   |
| 113 | automation trigger       |   |

Feedback
|-----|--------------------------|---|
| 200 |                          |   |
| 201 | play index               |   |
| 202 | automation shape name    |   |
| 203 | fx chain preset name     |   |
| 204 | lfo1 sync name           |   |
| 205 | lfo2 sync name           |   |
| 206 | automation time name     |   |
| 207 | time name                |   |
| 208 | automation x             |   |
| 209 | automation y             |   |
