## Info on commons

This are some random info on how to use the abstrations in the 'modules/commons' folder.

## Tempo sync

### Metronome

This is a metronome. When on it will output 24ppqn messages at the given time from the left outlet. The message contains the period value of the metronome. I.e. if the tempo is 120bpm, the left outlet will send a message containing the value 20.83333
every 20.83333ms. From the center outlet it will send a message containing a 0 when the metronome starts and stops.

### internal\_sync

This abstraction can be used to sync with an external metronome with a given subdivision. When the radio is on ext, the left outlet will send a message on time with the external time (if it is on) keeping a certain subdivision. The message will contain the period of that subdivision. I.e. if the external time is running at 120bpm and the internal\_sync is set to keep time at 1/4 note, the left output will send out a message containing the value 500 every 500ms. 

If the radio is set to internal, the same thing happens but the bpm followed can be set (it will not follow the external time) as well as the subdivision.

The third output is unused but must be left for compatibility.

### internal\_sync\_simple

It is nothing more than a metronome that can forward the external tempo or follow the internal one. In both cases it will output a message at a rate of 24ppqn (the message contains the period value).

## Routing audio

### four\_to\_one
### two\_to\_one
### one\_to\_four

## Spatialization

### quad\_simple\_automation

This receives 1 channel of audio and routes it to 4 outputs (FL, FR, RL, RR) in an automated way. You can choose the shape of the path, the time it takes
to get to the next point of the path and the spread of the path (how far is from the center). You can also choose what is the tempo/sync and the phase (where the path starts).

### four\_quad\_simple\_automation

This handles 4 automations at the same time, receiving 4 channels of audio and routing it to 4 outputs. 
#### Mono mode
It can be used in:
* **mono mode** where the 4 automations does the same and have the same phase (1,2,3 or 4). This is useful to route a 4 channel audio where all the channels
must be mixed togeter and moved through space. Use case: a monophonic synth with 4 oscillators (each detuned) on each channel that must be moved in the space as a whole.
* **poly mode** where the 4 automations does the same thing but have different phases. This is useful to route a 4 channel audio where each channel is moved
individually through the space. Use case: a polyphonic synth where each channel has a voice and each voice is moved in the space individually.

### quad\_pan

Takes 4 channel input and routes it to 4 outputs depending on x and y informations.

## Other

### lfo

The lfo has three outputs: 
* the first goes from 0 to 1 when depth is at max and is 0 when depth is at minimum
* the second goes from 1 to 0 when depth is at max and is 1 when depth is at minimum
* the third goes from -1 to 1 when depth is at max and is 0 when depth is at minimum

The factor set the depth range: it can be full (1/1), half (1/2) or a tenth (1/10). 

The lfo can be synced to the main tempo with a selected subdivision.

## Sounds

### wavefold

This is a module that does wavemangling. It has different modes: a new mode can be added!

### fx\_chain

This is a Kaoss pad effect. If it is created with a -1 argument **[fx\_chain -1]** it does not do audio computation. This is useful to use the module as master to control many instances.

## What uses what table

Every module keeps its abstraction in the 'module_name_abs' folder, even if they are abstraction taken from the commons. Yes, this creates duplicates but it has an advantage. 
In this way, when you edit a commons abstraction, you avoid messing up the modules because they use the abstraction present in its own folder. In fact, this can happen because the updated abstraction
has one more inlet or other things that needs to be handled in the parent module...
If you want to update the abstraction for the module, simply copy the edited abstraction in the commons folder to the 'module_name_abs' folder.

This shows what common abstraction is used by what module:

fm\_synth uses:
compressor
lfo
four\_quad\_pan\_automations
quad\_pan
adsr
vumeter
load\_preset
save\_preset
load\_init
save\_init

brds\_synth uses:
filter\_selection
rngs
wavefold
compressor
lfo
four\_quad\_pan\_automations
quad\_pan
adsr
vumeter
load\_preset
save\_preset
load\_init
save\_init

quad\_effects uses:
dry\_wet\_quad
four\_to\_one\_select
internal\_sync
lo\_hi\_filter
load\_preset
save\\_preset
load\_init
save\_init
lfo

drum\_machine uses
gauss\_rnd
internal\_sync
euclid
uziurn
filter\_selection
wavefold
lfo
quad\_pan\_automation
fx\_chain
rngs
quad\_pan


filter\_selection depencencies:
hp-filter
lp-filter
analog-filter
bob-filter
ladder-filter
bp-filter

fx\_chain dependencies:
fx\_babyscratch~
fx\_tremolo~
fx\_beat\_repeat~
fx\_loop~
compressor

lfo dependencies:
0\_to\_1\_log
internal\_sync

quad\_pan\_automation:
internal\_sync



