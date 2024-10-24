## brds_synth

This is a subtractive synth based on the MI Braids and the MI Rings modules (Mi4Pd). It has 2 LFOs: the first one is tied to the filter freq, the second one can be assigned.

### Sound generation

The synth has 4 output channels whose functioning is defined by three modes:
-> POLY: 8 voices of polyphony. Each voice will be sent sequentially to the 4 channels: the first to the first ch, the second to the second ch, ..., the fifth to the first ch again
-> POLY*: 4 voices. Eeach voice is composed by 2 oscillators that can be detuned. Each voice will be sent sequentially to the 4 channels, as above.
-> MONO: 1 voice. The voice is composed of 4 oscillators that can be detuned. The voice is spread on every channel: each channel receives one oscillator.

### Spatialization

Each channel can be assigned to one of the 4 output (FR, FL, RR, RL). This spatialization can be automatized using the spatial automation object.

When automation is set to poly mode, each channel is assigned to one output and moves according to the selected shape. This means that in (synth) poly mode,
each voice of a chord (up to 4) moves independently from the other whereas in (synth) mono mode each detuned oscillator moves independently from the other.

When automation is set to one of the other modes (1,2,3,4), all the channels moves in the space as a whole. This means that in (synth) poly mode 
all the voices are mixed and moves all together througout the space whereas in (synth) mono mode all the detuned oscillators are mixed and moves all together througout the space.

### Improve
To add poliphony:
* add two (!) brds\_full\_module~
* For poly mode 0:
    * Augment poly value (es. poly 10 1)
    * Add route number
    * Add the s\_to\_synth sender
    * Do +1 to detune sender (*40 becomes connected to 1,2,3,4,5 and *-1 connected to 6,7,8,9,10)
* For poly mode 1: 
    * Augment poly value (es. poly 10 1)
    * Add route
    * Add the two sender to the same route outlet
* For mono mode don't do anything


### To do:
-> LFO 2 assign
-> Extend poliphony. To do this simply add brds_synt_module objects, add send objects to note sending system and check the onoff.

### OSC implementation

Presets (ranges are 0-127 except where indicated)

| n | value |range|
|-----|--------------------------|---|
| 0   | timbre                     |                |
| 1   | colour                     |                |
| 2   | waveform select            |                |
| 3   | attack                     |                |
| 4   | decay                      |                |
| 5   | sustain                    |                |
| 6   | release                    |                |
| 7   | adsr curve                 |                |
| 8   | wavemangler amount         | From 0 to 1    |
| 9   | wavemangler type           |                |
| 10  | lfo1 freq                  |                |
| 11  | lfo1 depth                 |                |
| 12  | lfo1 waveform              |                |
| 13  | filter select              |                |
| 14  | filter freq                |                |
| 15  | filter q                   |                |
| 16  | rngs poly                  |                |
| 17  | rngs exciter onoff         |                |
| 18  | rngs pitch                 |                |
| 19  | rngs structure             |                |
| 20  | rngs position              |                |
| 21  | rnsg transposition         | From -21 to 21 |
| 22  | detune                     | From 0 to 1    |
| 23  |                            |                |
| 24  | lfo2 freq                  |                |
| 25  | lfo2 depth                 |                |
| 26  | lfo2 waveform              |                |
| 27  | lfo2 assign                |                |
| 28  | volume                     |                |
| 29  | filter attack              |                |
| 30  | filter decay               |                |
| 31  | filter sustain             |                |
| 32  | filter release             |                |
| 33  | lfo1 multiplier            |                |
| 34  | lfo2 multiplier            |                |
| 35  | mono mode                  |                |
| 36  | Wavemangler mix            | From 0 to 1    |
| 37  | automation sync            |                |
| 38  | automation division        |                |
| 39  | automation bpm             |                |
| 40  | automation shape           |                |
| 41  | automation transition time |                |
| 42  | automation spread          |                |
| 43  | automation phase           |                |
| 44  | rngs dry/wet               |                |
| 45  | rngs model                 |                |
| 46  | rngs damping               |                |
| 47  | rngs brightness            |                |
| 48  | rngs trigger onoff         |                |
| 49  | lfo1 sync                  |                |
| 50  | lfo1 phase                 |                |
| 51  | lfo2 sync                  |                |
| 52  | lfo2 phase                 |                |

Control

| n | value |range|
|-----|--------------------------|---|
| 100 | Waveform +                 |                |
| 101 | Waveform -                 |                |
| 102 | automation shape +         |                |
| 103 | automation shape -         |                |
| 104 | rngs trigger go            |                |
| 105 | automation trigger go      |                |
| 106 | 440 go                     |                |
| 107 | rngs transposition reset   |                |

Feedback

| n | value |range|
|-----|--------------------------|---|
| 200 | x1                         |                |
| 201 | y1                         |                |
| 202 | x2                         |                |
| 203 | y2                         |                |
| 204 | x3                         |                |
| 205 | y3                         |                |
| 206 | x4                         |                |
| 207 | y4                         |                |
| 208 | waveform name              |                |
| 209 | automation name            |                |
| 210 | lfo1 freq name             |                |
| 211 | lfo2 freq name             |                |
| 212 | automation sync name       |                |
| 213 |                            |                |
