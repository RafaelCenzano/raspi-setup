# raspi-setup
Documentation of setting up raspberry pi for club use

Note: This was originally tested and successfully shipped on the raspberry pi zero w.

## Initial setup

- Use the rasbian full desktop image for the operating system of the pi.
- Boot with a monitor, keyboard, and mouse connected and go through the rasbian setup.
- After rebooting, run `sudo apt-get update` then `sudo apt-get upgrade --fix-missing` to bring developer tools up to date.

## Configuration

- Run `sudo raspi-config` and update the tool.
- Next, expand the filesystem to make sure the pi can use the maximum amount of storage availble.
- Next, select `Interfacing Options` and enable ssh and vnc.
- Lastly, select `Advanced Options` and change the resolution to MODE 85 1280 x 720 60Hz 16:9.
