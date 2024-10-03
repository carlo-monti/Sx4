
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
  * The connection is handled by the object **[osc_connection]** in the main patch. The patch (the server) will listen on port 3000 and sends on 192.168.1.33:4000. If `localhost` is flagged, it will send on 127.0.0.1.

You can create as many sets as you want by simply repeating all of the above in a new folder. Remember that every instance of a module must have a different ID: if I want to load two `brds_synth` modules, each must have its own ID, otherwise the presets will be overwritten!



## Folder structure
This is the folder structure of the whole project. It is organized in such a way that every update or exporting sets is done by drop-in (copy-paste) a folder. 

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




## To do
* Fix dry wet flanger
* Fix flanger click (substitute guts with mine)
* Fix phases in square wave on LFO (there isn't any phase!)
* Fix noises when changing wave on phaser/flanger
* Add default preset for a module (i.e. load a standard piano sound...)
* Add fade-in function for the melodic_seq
* Build a drum machine that plays a quadraphonic sample (4 samples at a time)
* Check drum_machine sample rate
* Key tracking for the filter
* Check fm_synth adsr and substitute it with the one on the commons folder
* Fix reverse mode on phase_looper
* Fix hipass filter
* Fix all the randomization in drum_machine as a multiplication

## References
Sx4 is heavily based on the following projects:
* [Mi4Pd](https://github.com/TheTechnobear/Mi4Pd)
* [pd-mkmr](https://github.com/MikeMorenoDSP/pd-mkmr)
* [automatonism](https://www.automatonism.com/)
* [Luzha FX Punch-In](https://patchstorage.com/luzha-fx-punch-in-effect-board-for-pd/)
* [Fx-Chain patch](http://www.pdpatchrepo.info/hurleur/FX_Chain.zip)
* [DIY2](http://www.pdpatchrepo.info/hurleur/DIY2.zip)
* [CrossPomeha](https://patchstorage.com/crosspomeha-pd-version/)
