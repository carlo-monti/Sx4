
# Sx4

## What is it

Sx4 is a musical making system designed to exploit the power of a quadraphonic speaker setting. It is a kind of a framework: it is componed of many modules that can save presets globally and that can also be controlled via OSC. The modules are fully built instruments. You can think of it this way: Sx4 can be used to build a fixed instrument set (i.e. two subtractive synths, one fm synth, two sequencers, etc.) and use it to make songs that can be saved and recalled.  This project, so far, consist of the following modules:

* **melodic_seq**: is a melodic sequence generator. It can generate a random sequence of (max) 32 steps that can be varied througout the performance.

* **chords_seq**: is a chord sequence generator. It can generate a chord sequence of (max) 8 bars, add pattern and traspositions.

* **brds_synth**: is a polyphonic subtractive synth based on the Mutable Instruments Beards oscillator and on the Rings resonator. 

* **fm_synth**: is a polyphonic phase modulation synth: a clone of the DX7.

* **drum_machine**: is a 4 tracks sample based drum machine with a Kaoss Pad style xy effect.

* **quad_effects**: is a fx chain that includes: chorus, phaser, flanger, delays, granulator (M.I. Clouds) and reverbs.

## Getting started

This section illustrate how to start making music with Sx4. Let's create a simple monophonic generative set:
* **Create a new project**. Copy the `templates/set_template` folder in the `sets` folder and rename it as you want. This will be the folder of your new project. To open it simply open the `main.pd` file.
* **Add instruments to your project**. Load modules in the main patch.
   * Create a new object and type the name of the desidered module: `melody_seq`.
   * Create a **[main_connection 0]** objects and connect it to the first inlet of the module. This will assign ID 0 to it.
   * Do the same for creating a `brds_synth` module: create the object and assign to it ID 1.
   * Create a **[note_sender 1]** object and connect it to the first outlet of the `melody_seq` module. This will allow the module to be selected as source (from the note router) as source 1.
   * Create a **[note_receiver 1]** object and connect it to the first outlet of the connection object of the `brds_synth`. This will allow the module to be selected (from the note router) as recever 1.
   * Connect the 4 rightmost outlets of the `brds_synth` to the **[output]** object.
* **Start making music**.
   * Initialize the project: click on the `init` button.
   * Turn on the `melody_seq` by clicking on the radio on top left and select `internal`: this will start the generation of the notes following the internal tempo.
   * Assign the source 1 to the destination 1 in the note router matrix.
   * Turn on the main volume.
   * Now you should hear sound!
* **Add another synth**. This time we will load it in a subprocess.
   * Create a **[pd~ -ninsig 0 -noutsig 4]**.
   * Create a **[main_connection_ext 2 fm_synth_wrap.pd]** objects and connect it to the first inlet of the module. This will assign ID 2 to it.
   * Create a **[note_receiver 2]** object and connect it to the first outlet of the connection object of the `fm_synth`. This will allow the module to be selected (from the note router) as recever 2.
   * Connect the 4 outlet of the **[pd~]** object to the main out.
   * Re-initialize the project by clicking on the `init` button. This will start the new module in a new window.
   * Now you can select the source 1 also for this synth.
* **Save it**.
  * Click on the `save` button on the preset module. This will save this song as 1. You can later recall it by clicking on the load button.
* **Add OSC control**.
  * To control your patch via OSC you can assign the OSC name to a module using the connection object: **[main_connection 1 brds1]** will make the `brds_synth` responding on the `brds1` name. It means that   you can control the parameter number 21 by sending a value to `brds1/21`. You can find a parameter list with the corresponding number on the `OSC_implementation.txt` file in the `modules/info` folder. 

You can create as many sets as you want by simply repeating all of the above in a new folder. Remember that every instance of a module must have a different ID: if I want to load two `brds_synth` modules, each must have its own ID, otherwise the presets will be overwritten!

## Structure

The heart of the system is the `main.pd` patch. All the modules can be loaded directly here or as a subprocess using the **[pd~]** object. It is possible to load many instances of a module, each identified by an ID (this is needed to avoid overwriting presets) and by an OSC name (optional). The module is connected using a  **[main_connection]** object that takes the two previous parameters as argument. For example the module **[brds_synth]** connected with the **[main_connection 2 brds_synth_2]** object will receive OSC commands as `brds_synth_2` and has ID of 2. This means that it will save its preset in the folder `presets/brds_synth/2/`.

A main patch must contain this objects:

* **[osc_connection]**: this handles the OSC communication

* **[presets]**: this asks the other modules to load and save their preset. It also checks the last preset saved to avoid overwriting.

* **[note_router]**: this object receives notes from the generators and routes it to the connected instruments. It allows to select the source for a particular instrument (a instrument should have max 1 source).

* **[song_handler]**: this objects sets the global tempo and the global volume.

* **[output]**: this objects sends the 4 outputs of a module to the dac scaling the volume or merging to stereo.

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

Each module instance loads and saves its own presets in a dedicated folder that is named among its ID. I.e. an instance loaded with ID=12 in the set called `set_1` will save its presets in: `set_1/presets/folder/12`.  The request of loading and saving is sent globally by the **[presets]** object. In the main patch tha **[presets]** object will send the number of the preset that must be loaded (or saved) on  **[s to_all]** and it is forwarded to the modules via the main connection.

The presets are handled in each module via a **[pd handle_presets]** subpatch. Each parameter that should be saved has a number assigned and the module collect them and saves in a table that is written to a txt file. Similarly, to load a preset, the txt file is loaded into the table and the table is iterated to send the preset values to the patch via a router object. The routing of the iterated preset values is used also to control the parameters via OSC messages: i.e. an OSC message to control the gain (whose number is 32) is sent to the **[r control]** object, it is than routed as 32 and finally sent to the **[r preset_gain_$0]** object.

There are also some paramers that should not be saved but needs to be controlled via OSC (such as momentary buttons...). This is handled in the same way but in this case the object is called **[r ctrl_value]** and its code should start from 100. Similarly there are some informations that needs to be puts out without being saved. This are handled via ad-hoc configuration that receives message from the **[s fdbk_value]** object.

### Subprocess

Each module (usually the sound generators) can be loaded as a subprocess using the **[pd~]** object. In this case the connection object should be **[main_connection_ext]**. The first parameter is the path to the object and the other two parameters are the same: ID and OSC name. In this case the module should be loaded via its wrapping (i.e. `brds_synth_wrap.pd`) specifying the inlets and outlets on the **[pd~]** object. The wrapping receives messages on the **[r sub1]** object inside and outputs its (already formatted) OSC messages via the **[stdout]** object.

A wrapping can contain more than one module. In this case a **[main_connection_ext_dual]** object (or triple, ...) should be used. The first parameter is the path to the wrapping and the other parameters are a sequence of ID and OSC name. I.e. the object **[main_connection_ext_dual ./brds_synth/brds_synt_wrap_with_effects.pd 0 brds 2 brdseff]** loads the wrapping containing the brds synth and the quad effects modules. The brds synth will have a ID of 0 and receives the OSC messages as `brds` and the quad effects module will have a ID of 2 and receives the OSC messages as `brdseff`. Inside the wrapping the module should have objects such as **[r sub1]**, **[r sub2]**, ... and one **[stdout]** object.

The loading/saving of presets (or sequences...) is done with two objects:

* **[load_preset]**: this objects takes one or two creation arguments. The first one is the table name where the preset has to be written into (usually presets_$0). The second one (optional) is the preset file specification (i.e. `_seq`). To work properly it must be initialized with two parameters: the ID (2nd inlet) and the current_path (3rd inlet). The fourth inlet lets you set the filname specification.
As an example, the object **[load_preset presets_$0 _seq]**, with parameters ID = 2 and current_path = `/path/to/preset` works in this way: whenever it receives a number (say 5) on the first inlet it will read the file `/path/to/preset/2/5_seq.txt` into the table `presets_$0` and after that it will iterate the index-values pair of that table from the outlet.
Usually a sound module has a preset_$0 table and it will save only one preset in the folder identified by its ID (i.e. `/presets/2/5.txt` is the preset 5 of the module with ID 2). The generation module may need to store other things such as the sequence. This is done with another load_preset object that has a second argument that represent the filename specification: something that is appended to the txt filename (as in the example above).

* **[save_preset]**: this object takes two or three creation arguments. The first is the table name, the second one is the number of preset values that has to be saved and the third is the filename specification (optional). As the load_preset object, it needs ID and current_path as parameters. The fourth and the fifth inlets lets you set dinamically the number of presets and the filename specification. Whenever it receives a number in its 1st inlet, it will collect the preset values stored in the preset table. 

## Folder structure
This is the folder structure of the whole project. It is organized in such a way that every update or exporting sets is done by drop-in (copy-paste) a folder. 

### Modules folder
The `modules` folder contains the modules (i.e. `module_1.pd`) that can be loaded in the main patch and the wrappings (i.e. `module_1_wrap.pd`) to be loaded in a subprocess. This folder also contains subfolders for the abstractions required by each module (i.e. `module_1_abs`) and the `commons` folder that contains all the little abstractions shared by the modules (and also the mandatory modules used in the main patch: presets, routing, ect.).

### Init
When the init button is pressed (on the main patch):
* The current path is sent to all the **[main_connection]** objects (**[also main_connection_ext]**)
* The init bang is sent. It is received by the **[main_connection]** objects and by the preset object.
* When a **[main_connection]** object receives the init bang, it sends out the `osc_name` and the `current_path` to the module connected to it.
* When a **[main_connection_ext]** object receives the init bang, it will start the subprocess and after 2 seconds it will send out the `osc_name` and the `current_path` to the module connected to it (to allow the starting of the subprocess).
* When the preset object receives the init bang, it will wait 3 seconds and it will send out the **[load 0(** message.
  
### Sets folder
The `sets` folder contains a subfolder for each set. Each folder can be freely named and it must contain a `presets` folder in which all the instances of modules will store their presets.
```bash
Sx4
├── modules
│   ├── commons
│   ├── module_1_abs/
│   │   ├── abs1-1.pd
│   │   ├── abs1-2.pd
│   │   └── [...]
│   ├── module_2_abs/
│   ├── [...]
│   ├── module_1.pd
│   ├── module_2.pd
│   └── [...]
├── sets
│   ├── set_1
│   │   ├── main.pd
│   │   ├── control.tosc
│   │   └── presets
│   │       ├── last_saved_presets.txt
│   │       ├── 0
│   │       │   ├── 0.txt
│   │       │   ├── 1.txt
│   │       │   └── [...]
│   │       ├── 1
│   │       │   ├── 0.txt
│   │       │   ├── 0_seq.txt
│   │       │   └── [...]
│   │       ├── 2/
│   │       ├── 3/
│   │       ├── [...]
│   │       ├── 100/
│   │       ├── 101/
│   │       └── 102/
│   ├── set_2
│   └── [...]
└── templates
```
## TouchOSC

Sx4 can be used directly loading and controlling the PureData patch. It is also possible to use it in a client/server setting loading the patch without the GUI and using a OSC controller such as TouchOSC. In the main folder there is a `control.tosc` file that does this.

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

## To do
* Add default preset for a module (i.e. load a standard piano sound...)
* Add fade-in function for the melodic_seq

## References
Sx4 is heavily based on the following projects:
* [Mi4Pd](https://github.com/TheTechnobear/Mi4Pd)
* [pd-mkmr](https://github.com/MikeMorenoDSP/pd-mkmr)
* [automatonism](https://www.automatonism.com/)
* [Luzha FX Punch-In](https://patchstorage.com/luzha-fx-punch-in-effect-board-for-pd/)
* [Fx-Chain patch](http://www.pdpatchrepo.info/hurleur/FX_Chain.zip)
* [DIY2](http://www.pdpatchrepo.info/hurleur/DIY2.zip)
* [CrossPomeha](https://patchstorage.com/crosspomeha-pd-version/)
