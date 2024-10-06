## Modules

This are the modules that can be used in Sx4:

### Main Modules

* **main_connection**
* **main_connection_ext**
* **main_connection_ext_2**
* **main_connection_ext_3**
* **main_connection_ext_4**
* **main_note_sender**
* **main_note_receiver**
* **main_note_router**
* **main_midi_input**

### Modules

* **melodic_seq**: is a melodic sequence generator. It can generate a random sequence of (max) 32 steps that can be varied througout the performance.

* **chords_seq**: is a chord sequence generator. It can generate a chord sequence of (max) 8 bars, add pattern and traspositions.

* **brds_synth**: is a polyphonic subtractive synth based on the Mutable Instruments Beards oscillator and on the Rings resonator. 

* **fm_synth**: is a polyphonic phase modulation synth: a clone of the DX7.

* **drum_machine**: is a 4 tracks sample based drum machine with a Kaoss Pad style xy effect.

* **quad_effects**: is a fx chain that includes: chorus, phaser, flanger, delays, granulator (M.I. Clouds) and reverbs.

### Multiple Wrappings



### quad_effects

Quad_effects is a quadraphonic effect chain. It is composed of four sections: modulation, delay, granulation and reverb. The sections can be used in sidechain or as parallel sends.

MODULATION SECTION  *******************************
This section has a chorus and a modulator

CHORUS

MODULATOR
Select effect:
0-> off
1-> phaser
2-> flanger
3-> tremolo

Phase: define the phases of the 4 lfos.
0-> all have the same phase
1-> phases are even (0, 1/4, 1/2, 3/4)
2-> phases are odd

Time: define the time (when in absolute mode) or the subdivision of the master clock (when in synced mode)

Sync: define if the time Is free or synced with the master with a certain subdivision

Spread: define how the time is different between the 4 lfos
0-> all have the same time subdivision
1-> they are separated by 1 subdivision (if synced)
2-> they are separated by 2 subdivision (if synced)
...
If they are not synced, the time are continuously spaced by a certain amount (to do: set periodicity also in this case: half, quarter...)

Feedback:

Delay:

Dry/Wet:

DELAY SECTION *******************************

This is a quadraphonic delay (4 independent delays) that can be used in many ways.

Time: define the time (when in absolute mode) or the subdivision of the master clock (when in synced mode)

Sync: define if the time Is free or synced with the master with a certain subdivision

Spread: define how the time is different between the 4 delays.
0-> all have the same time subdivision
1-> they are separated by 1 subdivision (if synced)
2-> they are separated by 2 subdivision (if synced)
...
If they are not synced, the time are continuously spaced by a certain amount (to do: set periodicity also in this case: half, quarter...)

Feedback: amount of feedback into the delays

Filter: filter of the feedback. It is hi_pass when above half and low_pass when below half. It can be reset to center with the button.

Chain: define where the feedback of a delay is sent. I.e. "the output of the first delay goes to the feedback of the second".
0-> All delay feeds itself
1-> Infinite: 1->2->3->4
2-> Eight:
3-> Circle:

Diffusion: define if the output of a delay should follow strictly the chain or can be "diffused" also to other destination. I.e. if diffusion is set to high, the output of the first delay goes to the input of delay 2 but also a little to the input of 3 and 4 (depending on the selected chain).

Mono: all the inputs goes to the first delay (TO DO!)
