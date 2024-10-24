
# Sx4

## What is it

Sx4 is a musical making system designed to exploit the power of a quadraphonic speaker setting. It is a kind of a framework: it is componed of many modules that can save presets globally and that can also be controlled via OSC. Rather than a modular system (like AUTOMATONISM), it is intended as a traditional music making setup: the modules are be fully built instruments. You can think of it this way: Sx4 can be used to create a fixed instrument set (i.e. two subtractive synths, one fm synth, two sequencers, etc.) and use it to make songs that can be saved and recalled. Sx4 is something like a "live oriented" DAW: it allows you to build a dynamic structure of instruments whose initial settings are globally saved and that are intended to be "tweaked" during live performances.

## Getting started

This section illustrate how to start making music with Sx4. Let's create a simple monophonic generative set:
* **Create a new project**. Copy the `templates/set_template` folder in the `sets` folder and rename it as you want. This will be the folder of your new project. To open it simply open the `main.pd` file.
* **Add instruments to your project**. Load modules in the main patch.
   * Create a new object and type the name of the desidered module: `melody_seq`.
   * Create a **[main_connection 0]** objects and connect it to the first inlet of the module. This will assign ID 0 to it.
   * Do the same for creating a `brds_synth` module: create the object and assign to it ID 1.
   * Create a **[note_sender 1]** object and connect it to the first outlet of the `melody_seq` module. This will allow the module to be selected as source (from the note router) as source 1.
   * Create a **[note_receiver 1]** object and connect it to the first outlet of the connection object of the `brds_synth`. This will allow the module to be selected (from the note router) as recever 1.
   * Connect the 4 rightmost outlets of the `brds_synth` to the **[send_to_output]** object.
* **Start making music**.
   * Initialize the project: click on the `init` button.
   * Turn on the `melody_seq` by clicking on the radio on top left and select `int`: this will start the generation of the notes following the internal tempo.
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
  * The connection is handled by the object **[osc_connection]** in the main patch. The patch (the server) will listen on port 3000 and sends on 192.168.1.33:4000. If `localhost` is flagged, it will send on 127.0.0.1.
 
Now you can start making music with your new instrument set: find a sound, find a sequence, route it to the selected instrument... When you are happy, press the `save` button. This will save what you created with a number. You can recall it later by selecting the number and pressing the `load` button. 
From now on you should not modify your set because you will risk to overwrite what you already saved.

You can create as many sets as you want by simply repeating all of the above creating a new folder in the `sets` folder. Remember that every instance of a module must have a different ID: if I want to load two `brds_synth` modules, each must have its own ID, otherwise the presets will be overwritten!

## Dig deeper

The heart of the system is the `main.pd` patch within a set folder. All the modules can be loaded directly here or as a subprocess using the **[pd~]** object. It is possible to load multiple instances of a module, each identified by an ID (used to store presets) and by an OSC name (optional). The modules whose name starts with `main_` must be loaded only once in the main patch (no multiple instances!). 

Each module must be connected using a  **[main_connection]** object that takes the two previous parameters as argument. For example the module **[brds_synth]** connected with the **[main_connection 2 brds_synth_2]** object will receive OSC commands as `brds_synth_2` and has ID of 2 (this means that it will save its preset in the folder `presets/brds_synth/2/`). Moreover, each object should have a **[s osc_out]** object to its rightmost outlet. This allow the object to send out already formatted OSC messages to the OSC receiver.

### The main patch
The main patch must contain this objects:

* **[main_preset_handler]**: this asks the other modules to load and save their preset. It also checks the last preset saved to avoid overwriting.

* **[main_song_handler]**: this objects sets the global tempo and the global volume.

* **[main_output]**: this objects sends the 4 outputs of a module to the dac scaling the volume or merging to stereo.

* **[main_osc_handler]**: this handles the OSC communication (optional)

### What happens
Once the connections are done, you can press the `start everything` button in the main patch. This do this routine:

* It sends the current path to the connection object: this is the absolute path to the `main.pd` file. The connection objects forward this info to the modules already loaded in the main patch or uses it to build the path for loading the subprocess modules.

* It ask the connection_ext object to start the subprocesses

* It waits for 2000ms for the external modules to be loaded

* It sends the init message to all the connection objects in the main patch (and also to the all the other `main_` modules). The connection objects sends the `instance_id`, the `osc_name` and the `current_path` to the modules. It also sends the `load_init 0` message.

From now on, all the modules are ready and will receive commands from their **[main_connection]** object. This works this way:

* OSC messages: the messages from the network are collected by the **[main_osc_handler]**. This will send the OSC message to the **[main_connection]** objects that will forward them to the modules that will do the parsing.

* Tempo messages: the global `bang_bpm` and `rst` messages from the **[main_song_handler]** are sent to the **[main_connection]** objects that will forward them to the modules.

* Global volume: the `global_vol` and `global_merge` messages from the **[main_song_handler]** are sent to the **[output]** objects that will use it to scale the volume to the dac~ or to merge the channels.

* Presets: the `load` and `save` messages from the **[main_presets_handler]** are sent to the **[main_connection]** objects that will forward them to the modules.

The **[main_connection]** also allows the module to receive the messages that are sent via the **[s to_all]** object.

### Note generation

The modules that generates notes will output notes with midi note number and velocity, usually from their leftmosts outlet. For example **[60 106(** is a C4 note on message with velocity of 106 and **[69 0(** is a note off message for the midi note A4. The module can also produce a message  of **[all_notes_off(**. The rightmost outlet puts out (already formatted!) OSC messages.

The generation modules are usually kept in the `main.pd` patch and are connected to the **[note_router]** object via a **[note_sender]** object with its ID specified as first argument. Similarly a sound generation module receives notes via a **[note_receiver]** object (with ID). The router object will handle the routing. The **[note_receiver]** object should be connected on the first inlet of the **[main_connection]** object.

### Sound generation

The modules that receives or generates sound has generally four audio outputs. The audio outlets are usually put to the left while the rightmost outlet puts out (already formatted!) OSC messages. To output sound you should connect the 4 outputs of the module to the **[main_output]** object. This will forward the output directly to the dac. Be careful to avoid clipping by mixing many outlets! 

## Folder structure
This is the folder structure of the whole project. It is organized in such a way that every update or exporting sets is done by drop-in (copy-paste) a folder. 

```bash
Sx4
├── modules
│   ├── commons
│   ├── module_1_abs/
│   │   ├── factory_presets/
│   │   │   ├── 0.txt
│   │   │   ├── 1.txt
│   │   │   └── [...]
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

The `sets` folder contains a subfolder for each set. Each folder can be freely named and it must contain a `presets` folder in which all the instances of modules will store their presets. 

The `modules` folder contains the available modules and the wrappings to be loaded as subprocess. The modules that starts with `main_` are modules that must be used only in the main patch (they used global sends). Each module has a folder named `modules/module_name_abs` in which there are abstraction used by it, its factory presets and other similar things. The `modules/commons` folder contains abstractions that are used by all the modules.

The `templates` folder contains templates for building new modules.

## The modules

You can find a list of the modules [here](./docs/modules.md).

## Extending Sx4

You can find info on how to create new modules [here](./docs/improve.md).

## Client-server

You can find info on how to create a client-server configuration [here](./docs/client_server.md).

## Disclaimer

Sx4 is intended to be a fun experimentation and has nothing to do with auditory realism or scientific rigor: it is simply a framework for bouncing around sounds among four speakers in a creative manner. On the other hand, given that the actual spatialization is done by a bounch of abstractions, it should be simple to implement a more precise method (Ambisonics, …) of handling the sound positioning.

## References
Sx4 is heavily based on the following projects:
* [Mi4Pd](https://github.com/TheTechnobear/Mi4Pd)
* [pd-mkmr](https://github.com/MikeMorenoDSP/pd-mkmr)
* [automatonism](https://www.automatonism.com/)
* [Luzha FX Punch-In](https://patchstorage.com/luzha-fx-punch-in-effect-board-for-pd/)
* [Fx-Chain patch](http://www.pdpatchrepo.info/hurleur/FX_Chain.zip)
* [DIY2](http://www.pdpatchrepo.info/hurleur/DIY2.zip)
* [CrossPomeha](https://patchstorage.com/crosspomeha-pd-version/)
* [alexdrymonitis](https://github.com/alexdrymonitis/filter_abstractions)
