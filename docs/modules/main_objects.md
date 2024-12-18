## Main patch objects

Here are the specs for the objects that must be used in the main.pd patch.

### main_connection

This is used to connect modules within the main patch. It has ID and osc name as arguments: **[main_connection 1 synth]** will
have an ID of 1 and receives OSC messages as synth. A **[note_receiver]** can be connected above it (on its inlet).

### main_connection_ext_1

This is connected to a pd~ objects to connect modules that are loaded as a subprocess. It has ID and osc name as arguments: **[main_connection 1 synth]** will
have an ID of 1 and receives OSC messages as synth. A **[note_receiver]** can be connected above it (on its inlet).

### main_connection_ext_2 [...]

This is connected to a pd~ objects to connect multiple modules that are loaded as a subprocess within the same wrapping. 
It has many ID and osc name as arguments as modules inside the wrapping. If the object is **[main_connection 1 synth 2 synthfx]** the first
will module inside the wrapping will have an ID of 1 and receives OSC messages as synth whereas the second will have an ID of 2 and receives osc as synthfx. 
Many **[note_receiver]** can be connected above it (on its inlets).

### main_preset_handler

This is used to load and save presets. It must be connected with a main_connection object using an ID of 100 and (optional) an OSC
name of presets.

OSC implementation:

Control

| n | value |range|
|-----|--------------------------|---|
| 100 | Load preset button                  |   |
| 101 | Save preset button                  |   |
| 102 | Preset - button                     |   |
| 103 | Preset + button                     |   |
| 104 | Save currently loaded preset button |   |
| 105 | Check connection                    |   |

Feedback

| n | value |range|
|-----|--------------------------|---|
| 200 | Load preset number                  |   |
| 201 | Save preset as number               |   |
| 202 | Currently loaded preset number      |   |

### main_song_handler

This is used to edit global parameters for a particular song. It must be connected with a main_connection object using an ID of 101 and (optional) an OSC
name of song

OSC implementation:

Preset

| n | value |range|
|-----|--------------------------|---|
| 0   | Main volume                  	|   |
| 1   | Main bpm              |   |
| 2   | Merge outputs to stereo            |   |

Control

| n | value |range|
|-----|--------------------------|---|
| 100 | Metronome on/off toggle |   |


### main_osc_handler

This is used to send and receive osc messages. It must be connected with an **[s to_osc]** output at the bottom right.

### main_note_router

This is used to route notes to instruments within a particular set. It must be connected to a **[main_connection 102 router]**. 
It is used in combination with the **[main_note_sender]** and the **[main_note_receiver]**.

Preset

| n | value |range|
|-----|--------------------------|---|
|  0 | -                                 |   |
|  1 | Source selection for instrument 1 |   |
|  2 | Source selection for instrument 2 |   |
|  3 | Source selection for instrument 3 |   |
|  4 | Source selection for instrument 4 |   |
|  5 | Source selection for instrument 5 |   |
|  6 | Source selection for instrument 6 |   |
|  7 | Source selection for instrument 7 |   |
|  8 | Source selection for instrument 8 |   |


### main_midi_input

This is used to get midi note input. It should be connected to a **[note_sender]** object.

### main_output

This sends utput to the soundcard allowing main vulume control and stereo merge (4ch->2ch). This also sends out the vumeter.

Feedback

| n | value |range|
|-----|--------------------------|---|
| song/301 | Vumeter ch 1 (FL) |   |
| song/302 | Vumeter ch 2 (FR) |   |
| song/303 | Vumeter ch 3 (RL) |   |
| song/304 | Vumeter ch 4 (RR) |   |
