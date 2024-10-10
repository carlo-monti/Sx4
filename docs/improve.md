## Adding modules

To build a new module you can start from the template on the `templates` folder. 

### Inlets
Basically a module must have an inlet on the top. 
From this inlet the module will receive the global info from the main patch. He need to route it and use it somehow. Such infos are:
* **bang_bpm**: this is a number that represents the ms period of a 24ppqn beat for the global tempo. It is also sent with the same period! So if the global tempo is 120bpm the module will receive a `bang_bpm 20.83333` message every 20.83333 ms.
* **rst**: this is a number 0 sent when the global metronome starts or stops.
* **instance_id**: this is the instance number of the module. It must be sent to the `ID_$0` objects.
* **load**: this number is used to ask the module to load the preset.
* **save**: this number is used to ask the module to save the preset.
* **osc_name**: this is the string that the module will receive (and send) OSC messages on. 
* **osc**: this are the OSC messages. They must be filtered using the name above.
* **notes**: this are the notes in.
* **current_path**: this is the absoulute path of the main patch. It is used to handle the presets saving (it should be forwarded to the **[load_preset]** and **[save_preset]** objects).
* **init**: this is a message of 0 received from the main patch. It means that the module should put its configuration in the initial state. This is done by forwarding the 0 message to the **[load_init]** object.

### Outlets
A module can have audio outlets or message outlets. In addition, a module that implements OSC control must have the rightmost outlet dedicated to send out already formatted OSC messages.
If there are both messages and audio outputs, the audio outlets should come first, followed by the message outlets. The template already contains 4 audio outlets and a pre-built 
4ch panning system. The template also contains a pre-built osc formatting system that will output the messages using the OSC messages set when received in the input (via `osc_name` message).

### Presets
Each parameter that must be saved send a value that is received and store with a corresponding index in the preset table. The table can be saved and loaded using the **[load_preset]** and **[save_preset]** objects. Each module instance loads and saves its own presets in a dedicated folder that is named among its ID. I.e. an instance loaded with ID=12 in the set called `set_1` will save its presets in: `set_1/presets/folder/12`.  The request of loading and saving is sent globally by the **[presets]** object. In the main patch tha **[presets]** object will send the number of the preset that must be loaded (or saved) on  **[s to_all]** and it is forwarded to the modules via the main connection.

The presets are handled in each module via a **[pd handle_presets]** subpatch. Each parameter that should be saved has a number assigned and the module collect them and saves in a table that is written to a txt file. Similarly, to load a preset, the txt file is loaded into the table and the table is iterated to send the preset values to the patch via a router object. The routing of the iterated preset values is used also to control the parameters via OSC messages: i.e. an OSC message to control the gain (whose number is 32) is sent to the **[r control]** object, it is than routed as 32 and finally sent to the **[r preset_gain_$0]** object.

There are also some paramers that should not be saved but needs to be controlled via OSC (such as momentary buttons...). This is handled in the same way but in this case the object is called **[r ctrl_value]** and its code should start from 100. Similarly there are some informations that needs to be puts out without being saved. This are handled via ad-hoc configuration that receives message from the **[s fdbk_value]** object.

The loading/saving of presets (or sequences...) is done with two objects:

* **[load_preset]**: this objects takes one or two creation arguments. The first one is the table name where the preset has to be written into (usually presets_$0). The second one (optional) is the preset file specification (i.e. `_seq`). To work properly it must be initialized with two parameters: the ID (2nd inlet) and the current_path (3rd inlet). The fourth inlet lets you set the filname specification.
As an example, the object **[load_preset presets_$0 _seq]**, with parameters ID = 2 and current_path = `/path/to/preset` works in this way: whenever it receives a number (say 5) on the first inlet it will read the file `/path/to/preset/2/5_seq.txt` into the table `presets_$0` and after that it will iterate the index-values pair of that table from the outlet. The filename specification is used, for example, in the drum_machine where there are 4 sub-modules, each saving its own presets.

Usually a sound module has a preset_$0 table and it will save only one preset in the folder identified by its ID (i.e. `/presets/2/5.txt` is the preset 5 of the module with ID 2). The generation module may need to store other things such as the sequence. This is done with another load_preset object that has a second argument that represent the filename specification: something that is appended to the txt filename (as in the example above).

* **[save_preset]**: this object takes two or three creation arguments. The first is the table name, the second one is the number of preset values that has to be saved and the third is the filename specification (optional). As the load_preset object, it needs ID and current_path as parameters. The fourth and the fifth inlets lets you set dinamically the number of presets and the filename specification. Whenever it receives a number in its 1st inlet, it will collect the preset values stored in the preset table.

### Init and factory presets
A module should have a init configuration that is recalled when the `init` message is received. This is done using the **[load_init]** object. The first argument is the preset table name. The second argument is the folder where the 0.txt file is located (the `module_abs` folder). The third argument is the filename specification. I.e. a **[load_init presets_$0 brds_synth_abs]** object will load the file `modules/brds_synth_abs/0.txt` in the `presets_$0` folder. Again, a **[load_init presets_$0 brds_synth_abs _1]** object will load the file `modules/brds_synth_abs/0_1.txt` in the `presets_$0` folder: this is useful if the big module is made by small modules each saving its own presets. 

When the init button is pressed (on the main patch):
* The current path is sent to all the **[main_connection]** objects (**[also main_connection_ext]**)
* The init bang is sent. It is received by the **[main_connection]** objects and by the preset object.
* When a **[main_connection]** object receives the init bang, it sends out the `osc_name` and the `current_path` to the module connected to it.
* When a **[main_connection_ext]** object receives the init bang, it will start the subprocess and after 2 seconds it will send out the `osc_name` and the `current_path` to the module connected to it (to allow the starting of the subprocess).
* When the preset object receives the init bang, it will wait 3 seconds and it will send out the **[init 0(** message.

### Setup init

The init system can also be used to create some "factory presets" by simply creating other files `.txt` in the same folder and loading them. To save a configuration as preset (also as init: preset 0)
there is an object named **[save_init]**. Similarly to **[load_init]** it takes the preset table, the abs folder and the file specification as arguments. It also takes one more argument: the number of presets. I.e. the object **[save_init presets_$0 brds_synth_abs 34 _d]** when it receives in input the number 0, saves the file `0_d.txt` in the `brds_synth_abs/factory_presets` folder collecting 34 values from the table `presets_$0`. This object can be activated only opening the patch and clicking on the number message connected to it.

### Handle multiple submodules

As the `drum_machine` shows, a module can be built using multiple similar submodules. In this case the presets and the init should be handled carefully. 

Let's recap how the preset and init thing work in a simple module. In this case, when the module receives a `load_preset` or `save_preset` number on the first inlet, it will forward this number to the **[r load_$0]** or **[r save_$0]** objects and they will activate the object **[load_preset]** (or **[save_preset]**) that will save (or load) the preset file in the folder of the current set that has the name of the ID of the module. The loading of the init preset works in a similar way: a message of `load_preset 0` is received in the input, it is then forwarded to the object that will load the file from the `modules/module_name_abs/factory_preset` folder. The saving can be done only by opening the patch and clickin on the message.

When there are multiple submodules, the things are a little bit tricky. In this case the saving of the preset is done from the patch that join all the submodules. Here the message containing `0` is sent to the object **[r save_init_$0]** inside the preset handler subpatch (if any) and also forwarded as `save_init 0` to the submodules inlet. Here the message is routed to the same  **[r save_init_$0]** inside the preset handler subpatch of the submodule that will save it adding the `module_id` at the end of the filename. It also save the sequence in a different file. In the end the files inside the `modules/drum_machine_abs/factory_presets` are:
* 0.txt
* 0_1.txt
* 0_1_seq.txt
* 0_2.txt
* 0_2_seq.txt
* [...]
where `0.txt` is the preset file for the module, `0_1.txt` is the preset file for the submodule with the module_id of 1, `0_1_seq.txt` is the preset file for the sequence of the submodule 1, and so on...

### Subprocess and wrapping

Each module (usually the sound generators) can be loaded as a subprocess using the **[pd~]** object. In this case the connection object should be **[main_connection_ext]**. The first parameter is the path to the object and the other two parameters are the same: ID and OSC name. In this case the module should be loaded via its wrapping (i.e. `brds_synth_wrap.pd`) specifying the inlets and outlets on the **[pd~]** object. The wrapping receives messages on the **[r sub1]** object inside and outputs its (already formatted) OSC messages via the **[stdout]** object.

A wrapping can contain more than one module. In this case a **[main_connection_ext_dual]** object (or triple, ...) should be used. The first parameter is the path to the wrapping and the other parameters are a sequence of ID and OSC name. I.e. the object **[main_connection_ext_dual ./brds_synth/brds_synt_wrap_with_effects.pd 0 brds 2 brdseff]** loads the wrapping containing the brds synth and the quad effects modules. The brds synth will have a ID of 0 and receives the OSC messages as `brds` and the quad effects module will have a ID of 2 and receives the OSC messages as `brdseff`. Inside the wrapping the module should have objects such as **[r sub1]**, **[r sub2]**, ... and one **[stdout]** object.

### Where to put it
Once created the module must be positioned in the `modules` folder. This folder must also contains a subfolder (named `module_name_abs`) for the abstractions required. This subfolder should also contain another subfolder called `factory_presets` that should contain at least the `0.txt` file for the initialization (if any).

### Common objects

In the common folder there are a bunch of abstractions that can be used to build objects. Info can be found [./commons_abs.md](here).

## OSC control

The OSC implementation should follow some structure:

### TouchOSC

Sx4 can be used directly loading and controlling the PureData patch. It is also possible to use it in a client/server setting loading the patch without the GUI and using a OSC controller such as TouchOSC. In the main folder there is a `control.tosc` file that does this.

It has many pages, each corresponding to a module (more or less). To be easily modified, each controller object send out a OSC message composed as `osc_name/parameter_number`. The OSC name is built using `parent.name` parameter. In this way you can copy and paste the controller object (a slider, ...) in a different page that has a different name and edit only the number at the end of the OSC address.


## To do
* Move the osc parsing inside the main connection object (?)
* Fix dry wet flanger
* Add detune function for synths for using in combo. It will be dependent on ID: if even positive, if odd negative.
* Fix lfo assign in modules
* Fix flanger click (substitute guts with mine)
* Fix phases in square wave on LFO (there isn't any phase!)
* Fix noises when changing wave on phaser/flanger
* Add fade-in function for the melodic_seq
* Build a drum machine that plays a quadraphonic sample (4 samples at a time)
* Check drum_machine sample rate
* Key tracking for the filter
* Fix reverse mode on phase_looper
* Fix hipass filter
* Fix all the randomization in drum_machine as a multiplication
* Add swing to drum_machine
* Check kaoss fx drum machine



