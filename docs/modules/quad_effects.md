## quad_effects

Quad_effects is a quadraphonic effect chain. It is composed of four sections: modulation, delay, granulation and reverb. The sections can be used in sidechain or as parallel sends.

### Modulation
This section has a chorus and a modulator put in series.

#### Chorus

* **Rate**
* **Depth**
* **Wide**

#### Modulator

* **Select effect:** choose between: off, phaser, flanger, tremolo

* **Phase**: define the phases of the 4 lfos.
	* 0-> all have the same phase
	* 1-> phases are even (0, 1/4, 1/2, 3/4)
	* 2-> phases are odd

* **Time**: define the time (when in absolute mode) or the subdivision of the master clock (when in synced mode)

* **Sync**: define if the time Is free or synced with the master with a certain subdivision

* **Spread**: define how the time is different between the 4 lfos
	* 0-> all have the same time subdivision
	* 1-> they are separated by 1 subdivision (if synced)
	* 2-> they are separated by 2 subdivision (if synced)
	* ...
	If they are not synced, the time are continuously spaced by a certain amount

* **Feedback**:

* **Delay**:

* **Dry/Wet**:

### Delay

This is a quadraphonic delay (4 independent delays).

* **Time**: define the time (when in absolute mode) or the subdivision of the master clock (when in synced mode)

* **Sync**: define if the time Is free or synced with the master with a certain subdivision

* **Spread**: define how the time is different between the 4 delays.
	* 0-> all have the same time subdivision
	* 1-> they are separated by 1 subdivision (if synced)
	* 2-> they are separated by 2 subdivision (if synced)
	* ...
	If they are not synced, the time are continuously spaced by a certain amount (to do: set periodicity also in this case: half, quarter...)

* **Feedback**: amount of feedback into the delays

* **Filter**: filter of the feedback. It is hi_pass when above half and low_pass when below half. It can be reset to center with the button.

* **Chain**: define where the feedback of a delay is sent. I.e. "the output of the first delay goes to the feedback of the second".
	* 0-> All delay feeds itself
	* 1-> Infinite: 1->2->3->4
	* 2-> Eight:
	* 3-> Circle:

* **Diffusion**: define if the output of a delay should follow strictly the chain or can be "diffused" also to other destination. I.e. if diffusion is set to high, the output of the first delay goes to the input of delay 2 but also a little to the input of 3 and 4 (depending on the selected chain).

* **Mono**: all the inputs goes to the first delay (TO DO!)

### Granulator

The granulator is based on the MI Clouds (Mi4Pd).

* **Freeze**. This latching button stops the recording of incoming audio. Granularization is now performed on the last few seconds of audio kept in memory in the module.

* **Position**. Selects from which part of the recording buffer the audio grains are played. Turn the knob clockwise to travel back in time.

* **Size**.

* **Pitch**. At 12 o'clock, the buffer is played at its original frequency.

* **Density**. Determines how many grains are generated.

* **Constant**. If ON grains are generated at a constant rate. If OFF they are generated randomly.

* **Texture**. Morphs through various shapes of grain envelopes: square (boxcar), triangle, and then Hann window. Past 2 o'clock, activates a diffuser which smears transients.

* **Feedback**.

* **Reverb**.

* **Dry/wet**

### Reverb

* **Liveliness**
* **Lop**
* **Cutoff**
* **Dry wet**

### OSC implementation

Presets (ranges are 0-127 except where indicated)

| n | value |range|
|-----|--------------------------|---|
| 0   | chorus rate            |   |
| 1   | chorus wide            |   |
| 2   | chorus depth           |   |
| 3   | chorus dry/wet         |   |
| 4   | delay time spread      |   |
| 5   | delay mono             |   |
| 6   | delay chain            |   |
| 7   | delay sync onoff       |   |
| 8   | delay time             |   |
| 9   | delay diffusion        |   |
| 10  | delay feedback         |   |
| 11  | delay filter           |   |
| 12  | delay dry/wet          |   |
| 13  | grns texture           |   |
| 14  | grns mode              |   |
| 15  | grns constant          |   |
| 16  | grns position          |   |
| 17  | grns fbk               |   |
| 18  | grns density           |   |
| 19  | grns size              |   |
| 20  | grns reverb            |   |
| 21  | grns pitch             |   |
| 22  | grns dry/wet           |   |
| 23  | reverb liveliness      |   |
| 24  | reverb lop             |   |
| 25  | reverb freq            |   |
| 26  | reverb dry/wet         |   |
| 27  | global volume          |   |
| 28  | chain                  |   |
| 29  |                        |   |
| 30  |                        |   |
| 31  | phaser sync            |   |
| 32  | dry volume             |   |
| 33  | phaser phase           |   |
| 34  | phaser waveform        |   |
| 35  | phaser timespread      |   |
| 36  | phaser timespread      |   |
| 37  | phaser depth           |   |
| 38  | phaser dry/wet         |   |
| 39  | phaser feedback        |   |
| 40  | phaser delay feedback  |   |
| 41  | phaser choose fl ph tr |   |

Control

| n | value |range|
|-----|--------------------------|---|
| 100 | reset filter           |   |
| 101 | reset pitch            |   |
| 102 | grns freeze            |   |

Feedback

| n | value |range|
|-----|--------------------------|---|
| 200 | delay value            |   |
| 201 | phaser timespread      |   |

### To do
* Implement delay mono
* Implement different reverbs
* Add periodicity time ratio also when non in sync
