# Client-server settings


Here are the instruction to build a headless server that runs Sx4 patch controlled by a client that runs TouchOSC. The client will be a Raspberry Pi and will be connected to the server via router: 
* The client IP is  `192.168.0.22` and will transmit on port `3000` and receive on port `3000`.
* The server IP will be  `192.168.0.33 ` and will transmit on port `3000` and receive on port `3000`.

## Server
* Install Debian without any DE.
* Install sudo: `apt install sudo`
* Add username to sudoers `sudo usermod -aG sudo [username]`
* Add username to audio group (if not already present) `sudo usermod -a -G audio [username]`
* Install liquorix kernel `curl -s 'https://liquorix.net/install-liquorix.sh' | sudo bash`

* Set audio group limits [(ref)](http://tedfelix.com/linux/linux-midi.html):
    * `sudo mkdir /etc/security/limits.d`
    * `sudo nano /etc/security/limits.d/audio.conf`
    * Add lines:
      ```bash
      @audio   -  rtprio     95
      @audio   -  memlock    unlimited
      #@audio   -  nice       -19
      ```
* Install jack `sudo apt install jackd2`

* Use `jack_control` to setup audio I/O:
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

## Client
This are the info on how to use a Raspberry PI 4 with a touchscreen. The Rpi will be connected to a router via ethernet with a static IP of  `192.168.0.33/24`.

* Install Raspbian OS
* User `sx4` and pwd `sx4`
* Download `.deb` file from http://hexler.net (ARM 64)
* Install TouchOSC `sudo dpkg -i filename.deb`
* Disable all the services that are not needed:
  * `sudo systemctl disable bluetooth`
  * `sudo systemctl disable cups`
  * `sudo systemctl disable cups-browsed`
  * `sudo systemctl disable cron`
  * `sudo systemctl disable ssh`
* Setup the ethernet connection:
  ```bash
  nmcli con add con-name sx4_client type ethernet
  nmcli con modify sx4_client ipv4.address 192.168.0.33
  nmcli con modify sx4_client ipv4.method manual
  ```
* Create a script: `nano ./sx4_client.sh`.
* Make it executable `sudo chmod +x sx4_client.sh`
* The script will disable wifi, start ethernet connection and load TouchOSC
   ```bash
   #!/bin/bash
   # Disable Wifi
   sudo rfkill block all`
   # Turn up sx4 connection
   nmcli con up sx4_client
   # Start OSC full screen opening control.tosc file
   /opt/touchosc/TouchOSC control.tosc
   ```
* Make the script autorun on startup
   * Create an autostart directory (if one does not already exist): `mkdir /home/sx4/.config/autostart`
   * Create a .desktop file inside it:
   `nano /home/pi/.config/autostart/sx4.desktop`
   * Edit the file:
   ```bash
   [Desktop Entry]
   Type=Application
   Name=sx4_client
   Exec=/home/sx4/sx4_client.sh
   ```
* Set up TouchOSC connection to have the IP and port set correcty

## How to use

Once all the connections have been made, you should first start the client and then start the patch with the argument  `-send ";auto_start 1"`. This will make the patch start all the subprocesses and send `init 0` message to all of them. Wait 4 seconds and the set is ready to use.

###
