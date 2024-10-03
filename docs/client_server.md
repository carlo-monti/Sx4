
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
