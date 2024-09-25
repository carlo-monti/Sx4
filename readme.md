
# Sx4

## What is it

Sx4 is a musical making system designed to exploit the power of a quadraphonic speaker setting. It is a kind of a framework: it is componed of many modules that can save presets globally and that can also be controlled via OSC. The modules are fully built instruments. You can think of it this way: Sx4 can be used to build a fixed instrument set (i.e. two subtractive synths, one fm synth, two sequencers, etc.) and use it to make songs that can be saved and recalled.  This project, so far, consist of the following modules:

* **melodic_seq**: is a melodic sequence generator. It can generate a random sequence of (max) 32 steps that can be varied througout the performance.

* **chords_seq**: is a chord sequence generator. It can generate a chord sequence of (max) 8 bars, add pattern and traspositions.

* **brds_synth**: is a polyphonic subtractive synth based on the Mutable Instruments Beards oscillator and on the Rings resonator. 

* **fm_synth**: is a polyphonic phase modulation synth: a clone of the DX7.

* **drum_machine**: is a 4 tracks sample based drum machine with a Kaoss Pad style xy effect.

* **quad_effects**: is a fx chain that includes: chorus, phaser, flanger, delays, granulator (M.I. Clouds) and reverbs.

## Structure

The heart of the system is the `main.pd` patch. All the modules can be loaded directly here or as a subprocess using the **[pd~]** object. It is possible to load many instances of a module, each identified by an ID (this is needed to avoid overwriting presets) and by an OSC name (optional). The module is connected using a  **[main_connection]** object that takes the two previous parameters as argument. For example the module **[brds_synth]** connected with the **[main_connection 2 brds_synth_2]** object will receive OSC commands as `brds_synth_2` and has ID of 2. This means that it will save its preset in the folder `presets/brds_synth/2/`.

In the main patch there must be this objects:

* **[osc_connection]**: this handles the OSC communication

* **[presets]**: this asks the other modules to load and save their preset. It also checks the last preset saved to avoid overwriting.

* **[note_router]**: this object receives notes from the generators and routes it to the connected instruments. It allows to select the source for a particular instrument (a instrument should have max 1 source).

* **[song_handler]**: this objects sets the global tempo and the global volume.

## Modules

A module is a patch that generates notes (such a melody or a chord sequence) or that generates sound. Each module should have a **[main_connection]** object connected to its inlet. This sets the ID and the OSC name of the module. It also allow the module to receive the messages that are sent via the **[s to_all]** object: this messages are:

* **bang_bpm**: a number representing the midi_clock period of the main clock (24 ppqn). It acts also as a midi_clock pulse.

* **rst**: a `0` sent whenever the clock starts.

* **load** or **save**: a number that represents the preset to load (or save).

The **[main_connection]** object also allow the receipt of the notes and the OSC messages. Moreover, each object should have a **[s osc_out]** object to its rightmost outlet. This allow the object to send out already formatted OSC messages to the OSC receiver.

### Messages

Each module receives messages from a single inlet and routes it: a bang_bpm, a note, an OSC message... In the top left of each patch there should be objects to do this. Similarly in the bottom right there should be something that puts out the formatted OSC messages.

### Note generation

The modules that generates notes will output notes with midi note number and velocity, usually from their leftmosts outlet. For example **[60 106(** is a C4 note on message with velocity of 106 and **[69 0(** is a note off message for the midi note A4. The module can also produce a message  of **[all_notes_off(**. The rightmost outlet puts out (already formatted!) OSC messages.

The generation modules are usually kept in the `main.pd` patch and are connected to the **[note_router]** object via a **[note_sender]** object with its ID specified as first argument. Similarly a sound generation module receives notes via a **[note_receiver]** object (with ID). The router object will handle the routing. The **[note_receiver]** object should be connected on the first inlet of the **[main_connection]** object.

### Sound generation

The modules that receives or generates sound has generally four audio outputs that should be individually connected to the **[s~ to_dac_1]** (or 2, or 3, ...) object. Be careful to avoid clipping by mixing many outlets! The audio outlets are usually put to the left while the rightmost outlet puts out (already formatted!) OSC messages.

### Presets

Each module loads and saves its own presets in a dedicated folder but the request of loading and saving is sent globally by the **[presets]** object. It will send the number of the preset that must be loaded (or saved) on the **[s to_all]** object. Each module has its own preset folder (placed in the `presets`) folder and each instance saves its preset in the ID subfolder of it: the instance of brds synth with ID of 3 will save its preset in the `presets/brds_presets/3/` folder.

The presets are handled in each module via a **[pd preset_handler]** subpatch. Each parameter that should be saved has a number assigned and the module collect them and saves in a table that is written to a txt file. Similarly, to load a preset, the txt file is loaded into the table and the table is iterated to send the preset values to the patch. The routing of the iterated preset values is used also to control the parameters via OSC messages: a OSC message is sent to the **[r control]** object and sent to the **[r preset_value]**.

There are also some paramers that should not be saved but needs to be controlled via OSC (such as momentary buttons...). This is handled in the same way but in this case the object is called **[r ctrl_value]** and its code should start from 100. Similarly there are some informations that needs to be puts out without being saved. This are handled via ad-hoc configuration that receives message from the **[s fdbk_value]** object.

### Subprocess

Each module (usually the sound generators) can be loaded as a subprocess using the **[pd~]** object. In this case the connection object should be **[main_connection_ext]**. The first parameter is the path to the object and the other two parameters are the same: ID and OSC name. In this case the module should be loaded via its wrapping (i.e. `brds_synth_wrap.pd`) specifying the inlets and outlets on the **[pd~]** object. The wrapping receives messages on the **[r sub1]** object inside and outputs its (already formatted) OSC messages via the **[stdout]** object.

A wrapping can contain more than one module. In this case a **[main_connection_ext_dual]** object (or triple, ...) should be used. The first parameter is the path to the wrapping and the other parameters are a sequence of ID and OSC name. I.e. the object **[main_connection_ext_dual ./brds_synth/brds_synt_wrap_with_effects.pd 0 brds 2 brdseff]** loads the wrapping containing the brds synth and the quad effects modules. The brds synth will have a ID of 0 and receives the OSC messages as `brds` and the quad effects module will have a ID of 2 and receives the OSC messages as `brdseff`. Inside the wrapping the module should have objects such as **[r sub1]**, **[r sub2]**, ... and one **[stdout]** object.

## TouchOSC

Sx4 can be used directly loading the PureData patch. It is also possible to use it in a client/server setting loading the patch without the GUI and using a OSC controller such as TouchOSC. In the main folder there is a `control.tosc` file that does this.

It has many pages, each corresponding to a module (more or less). To be easily modified, each controller object send out a OSC message composed as `osc_name/parameter_number`. The OSC name is built using `parent.name` parameter. In this way you can copy and paste the controller object (a slider, ...) in a different page that has a different name and edit only the number at the end of the OSC address.

## The server

Here are the instruction to build a headless server that runs Sx4. It has been tested on Debian 12.

* Install Debian without any DE.

* Install sudo: `apt install sudo`

* Add username to sudoers `sudo usermod -aG sudo username`

* Add username to audio group (if not already present)

* Install liquorix kernel `curl -s 'https://liquorix.net/install-liquorix.sh' | sudo bash`

* Set audio group limits:
    * `sudo mkdir /etc/security/limits.d`
    * sudo nano /etc/security/limits.d/audio.conf
    * Add lines:`@audio   -  rtprio     95 @audio   -  memlock    unlimited #@audio   -  nice       -19`
* Install jack `sudo apt install jackd2`

* Use jack_control to setup audio I/O

* Launch the patch `pd -nogui -jack -outchannels 4 main.pd`
Quad project is a framework for making music exploiting the power of a quadraphonic speaker configuration. It is constituted
by modules that produces or process spatialized sound that are completely compatible with Pure Data Vanilla. 
The main.pd file is the entry point. 
