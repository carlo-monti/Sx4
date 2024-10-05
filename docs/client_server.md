
## Client-server settings

Here are the instruction to build a headless server that runs Sx4 controlled by a client that runs TouchOSC. It has been tested on Debian 12.

### On the server
* Install Debian without any DE.
* Install sudo: `apt install sudo`
* Add username to sudoers `sudo usermod -aG sudo [username]`
* Add username to audio group (if not already present) `sudo usermod -a -G audio [username]`
* Install liquorix kernel `curl -s 'https://liquorix.net/install-liquorix.sh' | sudo bash`

* Set audio group limits ([ref](http://tedfelix.com/linux/linux-midi.html):
    * `sudo mkdir /etc/security/limits.d`
    * `sudo nano /etc/security/limits.d/audio.conf`
    * Add lines:
      ```bash
      @audio   -  rtprio     95
      @audio   -  memlock    unlimited
      #@audio   -  nice       -19
      ```
* Install jack `sudo apt install jackd2`

* Use jack_control to setup audio I/O:
   * Check your soundcard. `aplay -l` will give you info about soundcards. Be careful: they are dynamically created during load. If you intend to create a script, you should use aliases obtained with `aplay -L` that should sound like `CARD=CK804,DEV=0`
   * Setup jack.
     ```bash
     jack_control stop
     jack_control ds alsa
     jack_control dps device hw:CARD=CK804,DEV=0
     jack_control dps capture hw:CARD=CK804,DEV=0
     jack_control dps playback hw:CARD=CK804,DEV=0
     jack_control dps rate 44100
     jack_control dps nperiods 3
     jack_control dps inchannels 18
     jack_control dps outchannels 18
     jack_control dps period 128
     jack_control dps duplex true
     jack_control dps midi-driver seq
     jack_control start
     ```

* Launch the patch `pd -nogui -jack -send ";auto_start 1" -outchannels 4 path/to/main.pd`
