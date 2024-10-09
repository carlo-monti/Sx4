# phase_looper
phase_looper is a looper inspired from the SOMA cosmos. It will record a slice of audio and repeat the playback with 4 different length (creating a phasing effect).

## how the code works
The sound is recorded into a table and there are 4 readers that will read it with different length.

### Mode 1
In mode 1 the table is played in its full length from start to end (or in reverse) at a different speed depending on the octave.

Inside pd main_values:

When the reader receives the loop length (global) it will scale it according to its ID (0 to 3: 100, 90%, 80%, 70% of the original loop length) and according to the octave selected (longer or shorter). This will set the internal envelope length and the phasor frequency (to match the octave). It means that the phasor will read the table at a certain speed to match the octave and the sound will be scaled in amplitude by the envelope. After the envelope has finished, it will reset again the phasor phase and the playing will restart from the beginning.

Inside pd build_envelope:
The envelope length will generate the attack decay and sustain length (in ms). The envelope will calculate the attack/decay values giving the shape of a saw, of an inverted saw or a mountain. When envelope is at 64 (center), the envelope has a fixed attack and decay (4 ms): it is played exactly as a looper will do (only fading out the edges).

Inside pd retrigger_envelope:
When a play (1) message is received, the attack sustain and decay values are collected, a reset message is sent to the phasor and the envelope starts. It is retrigged after a while (a time proportional to the wait value).

### Mode 2 (overlapping)

In mode 2 the table is divided in two equal parts, each having a simmetric envelope (attack=decay). The attack/decay time is where the overlapping of the two happens: one table is played and when the decay starts, the second table attack starts (fade out / fade in). The length of the attack/decay is the amount of overlapping.

In this mode the wait doesn't work.

Inside pd build_envelope:
The table length is divided in half, giving the envelope length. The attack/decay value is calculated in percentage of the 1/4 of the full table length. When env shape is max, the attack/decay is exactly half of the envelope length (1/4 of the full table).



