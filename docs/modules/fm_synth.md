## fm_synth
This is a phase-modulation synth, based on the Dx7 clone by Really Useful Plugins. It has 6 operators and 1 low pass filter.

###Sound generation

The synth has 4 output channels whose functioning is defined by three modes:
-> POLY: 8 voices of polyphony. Each voice will be sent sequentially to the 4 channels: the first to the first ch, the second to the second ch, ..., the fifth to the first ch again
-> POLY*: 4 voices. Eeach voice is composed by 2 oscillators that can be detuned. Each voice will be sent sequentially to the 4 channels, as above.
-> MONO: 1 voice. The voice is composed of 4 oscillators that can be detuned. The voice is spread on every channel: each channel receives one oscillator.

###Spatialization

Each channel can be assigned to one of the 4 output (FR, FL, RR, RL). This spatialization can be automatized using the spatial automation object.

When automation is set to poly mode, each channel is assigned to one output and moves according to the selected shape. This means that in (synth) poly mode,
each voice of a chord (up to 4) moves independently from the other whereas in (synth) mono mode each detuned oscillator moves independently from the other.

When automation is set to one of the other modes (1,2,3,4), all the channels moves in the space as a whole. This means that in (synth) poly mode 
all the voices are mixed and moves all together througout the space whereas in (synth) mono mode all the detuned oscillators are mixed and moves all together througout the space.

### Improve
To add poliphony:
* add two (!) dx7Poly with all the connections
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

### To do

### OSC implementation

Presets (ranges are 0-127 except where indicated)

| n | value |range|
|-----|--------------------------|---|
| 0   | op1 ratio                |   |
| 1   | op1 level                |   |
| 2   | op1 attack               |   |
| 3   | op2 decay                |   |
| 4   | op1 sustain              |   |
| 5   | op1 release              |   |
| 6   | op2 ratio                |   |
| 7   | op2 level                |   |
| 8   | op2 attack               |   |
| 9   | op2 decay                |   |
| 10  | op2 sustain              |   |
| 11  | op2 release              |   |
| 12  | op3 ratio                |   |
| 13  | op3 level                |   |
| 14  | op3 attack               |   |
| 15  | op3 decay                |   |
| 16  | op3 sustain              |   |
| 17  | op3 release              |   |
| 18  | op4 ratio                |   |
| 19  | op4 level                |   |
| 20  | op4 attack               |   |
| 21  | op4 decay                |   |
| 22  | op4 sustain              |   |
| 23  | op4 release              |   |
| 24  | op5 ratio                |   |
| 25  | op5 level                |   |
| 26  | op5 attack               |   |
| 27  | op5 decay                |   |
| 28  | op5 sustain              |   |
| 29  | op5 release              |   |
| 30  | op6 ratio                |   |
| 31  | op6 level                |   |
| 32  | op6 attack               |   |
| 33  | op6 decay                |   |
| 34  | op6 sustain              |   |
| 35  | op6 release              |   |
| 36  | feedback                 |   |
| 37  | algorithm                |   |
| 38  | mono                     |   |
| 39  |                          |   |
| 40  | volume                   |   |
| 41  | op1 modulation           |   |
| 42  | op2 modulation           |   |
| 43  | op3 modulation           |   |
| 44  | op4 modulation           |   |
| 45  | op5 modulation           |   |
| 46  | op6 modulation           |   |
| 47  | lfo frequency            |   |
| 48  | lfo depth                |   |
| 49  | lfo waveform             |   |
| 50  | lfo multiplier           |   |
| 51  | automation sync          |   |
| 52  | automation division      |   |
| 53  | automation bpm           |   |
| 54  | automation shape         |   |
| 55  | automation transition    |   |
| 56  | automation spread        |   |
| 57  | op1 detune               |   |
| 58  | op2 detune               |   |
| 59  | op3 detune               |   |
| 60  | op4 detune               |   |
| 61  | op5 detune               |   |
| 62  | op6 detune               |   |
| 63  | filter freq              |   |
| 64  | automation phase         |   |
| 65  | automation trigger onoff |   |
| 66  | lfo sync                 |   |
| 67  | lfo phase                |   |

Controls

| n | value |range|
|-----|--------------------------|---|
| 100 | op1 reset                |   |
| 101 | op2 reset                |   |
| 102 | op3 reset                |   |
| 103 | op4 reset                |   |
| 104 | op5 reset                |   |
| 105 | op6 reset                |   |
| 106 | automation shape +       |   |
| 107 | automation shape -       |   |
| 108 | randomization go         |   |
| 109 | randomization select     |   |
| 110 | no detune                |   |
| 111 | no feedback              |   |
| 112 | randomize algorithm go   |   |
| 113 | 440 go                   |   |

Feedback

| n | value |range|
|-----|--------------------------|---|
| 200 |                          |   |
| 201 | lfo time name            |   |
| 202 | automation shape name    |   |
| 203 | x 1                      |   |
| 204 | y 1                      |   |
| 205 | x 2                      |   |
| 206 | y 2                      |   |
| 207 | x 3                      |   |
| 208 | y 3                      |   |
| 209 | x 4                      |   |
| 210 | y 4                      |   |
| 211 | automation sync name     |   |
