## Adding modules

To build a new module you can start from the template on the `templates` folder. 

### Inlets
Basically a module must have an inlet on the top. 
From this inlet the module will receive the global info from the main patch. He need to route it and use it somehow. Such infos are:
* **bang_bpm**
* **rst**
* **instance_id**
* **load**
* **save**
* **osc_name**
* **osc**
* **notes**
* **current_path**: this is the absoulute path of the main patch. It is used to handle the presets (it should be forwarded to the **[load_preset]** and **[save_preset]** objects).
* **init**: this is a message of 0 received from the main patch. It means that the module should put its configuration in the initial state. This is done by forwarding the 0 message to the **[load_init]** object.
Not all of this messages must be implemented.

### Outlets
A module can have audio outlets or message outlets. In addition, a module that implements OSC control must have the rightmost outlet dedicated to send out already formatted OSC messages.
If there are both messages and audio outputs, the audio outlets should come first, followed by the message outlets. The template already contains 4 audio outlets and a pre-built 
4ch panning system. The template also contains a pre-built osc formatting system that will output the messages using the OSC messages set when received in the input (via `osc_name` message).

### Init
A module should have a init configuration that is recalled when the `init` message is received. This is done using the **[load_init]** object. The first argument
is the preset table name, the second argument is the folder where the 0.txt file is located (the `module_abs` folder). I.e. a **[load_init presets_$0 brds_synth_abs]**
object will 


