## Info on commons

This are some random info on how to use the abstrations in the 'modules/commons' folder.

## Tempo sync

### Metronome

This is a metronome. When on it will output 24ppqn messages at the given time from the left outlet. The message contains the period value of the metronome. I.e. if the tempo is 120bpm, the left outlet will send a message containing the value 20.83333
every 20.83333ms. From the center outlet it will send a message containing a 0 when the metronome starts and stops.

### internal_sync

This abstraction can be used to sync with an external metronome with a given subdivision. When the radio is on ext, the left outlet will send a message on time with the external time (if it is on) keeping a certain subdivision. The message will contain the period of that subdivision. I.e. if the external time is running at 120bpm and the internal_sync is set to keep time at 1/4 note, the left output will send out a message containing the value 500 every 500ms. 

If the radio is set to internal, the same thing happens but the bpm followed can be set (it will not follow the external time) as well as the subdivision.

The third output is unused but must be left for compatibility.

### internal_sync_simple

It is nothing more than a metronome that can forward the external tempo or follow the internal one. In both cases it will output a message at a rate of 24ppqn (the message contains the period value).

## Spatialization



## Other

### lfo

The lfo has three outputs: 
* the first goes from 0 to 1 when depth is at max and is 0 when depth is at minimum
* the second goes from 1 to 0 when depth is at max and is 1 when depth is at minimum
* the third goes from -1 to 1 when depth is at max and is 0 when depth is at minimum

The factor set the depth range: it can be full (1/1), half (1/2) or a tenth (1/10). 

The lfo can be synced to the main tempo with a selected subdivision.