# raspi-setup
Documentation of setting up raspberry pi for [club](https://www.lowelldev.club) use. Conencting and access to the pi works on most computers including chromebooks.

Note: This was originally tested and successfully shipped on the raspberry pi zero w.

## Initial setup

- Use the rasbian full desktop image for the operating system of the pi.
- Boot with a monitor, keyboard, and mouse connected and go through the rasbian setup.
- After rebooting, run `sudo apt-get update` then `sudo apt-get upgrade --fix-missing` to bring developer tools up to date.
- Make sure when you run `python3 -V` the version is 3.6 or above. 

## Configuration

- Run `sudo raspi-config` and update the tool.
- Next, expand the filesystem to make sure the pi can use the maximum amount of storage availble.
- Next, select `Interfacing Options` and enable ssh and vnc.
- Lastly, select `Advanced Options` and change the resolution to ypur preffered resolution. You might have to test this as different pi's have different amounts of proccessing power and memory. For the raspberry pi zero recommend using `MODE 85` 1280 x 720 60Hz 16:9.

## Main setup

- Signup for a free account on [ngrok.com](https://ngrok.com/)
- Download the linux-arm (The proccessor the pi zero is 32, if using a 64 bit proccessor download the 64 bit version) ngrok zip file and place it in your home directory.
- Run `unzip` and file name of the zip you just downloaded.
- Next, run `./ngrok authtoken TOKENHERE` and replace `TOKENHERE` with your given authtoken
- This should generate a configuration file at `/home/USER/.ngrok2/ngrok.yml` (replace USER with yout username), open the file and add the following configurations (the tunnels names can be changed, You can't create more then 2 http tunnels, or 1 tcp and 1 http tunnel, if you go over ngrok blocks it from running because it will create too many tunnels on a free account):
```
tunnels:
  flaskhttp:
    proto: http
    addr: 8000
  pissh:
    proto: tcp
    addr: 22
```
- Run `mkdir devclubpi` and `cd devclubpi`.
- Run `git clone https://github.com/lowell-dev-club/pi-ssh-vnc.git` and `cd pi-ssh-vnc`.
- Create a gmail app password. [Instructions here](https://support.google.com/accounts/answer/185833?hl=en). Create an app password for gmail and no specific device.
- Next create the file `config.py` in the `pi-ssh-vnc` directory and write this code and replace the place holders:
```
emailUser = 'EMAIL@DOMAIN.COM'
emailPass = 'YOUR APP PASSWORD'
```
- Lastly edit the rc.local file located at `/etc/rc.local`. This can be done with `sudo vi /etc/rc.local` or with your favorite editor. But it is required to add sudo as the file is under root priviledges. Add these lines above the `exit 0`, make sure to edit and correct any paths that may be different for your pi:
```
# HEADLESS vnc server setup
if /usr/bin/pgrep -U pi vncserver &gt;/dev/null ; then 
        /bin/sh -c '/usr/bin/sudo -u pi /usr/bin/vncserver -kill :1 &gt;/dev/null 2&gt;&;1 || :'
fi 
/bin/sh -c '/usr/bin/sudo -u pi /usr/bin/vncserver'

# Portforwarding
sudo -u pi /home/pi/ngrok start --all &
python3 /home/pi/devclubpi/pi-ssh-vnc/pi_ssh_vnc.py &
```
- Now as long as everything was setup properly and all software is downloaded and up to date your pi is now accessible from anywhere.
- To test this or to use it remotely run `reboot` and check the email you used for `pi-ssh-vnc`. This also works if you unplug everything from the pi except the power cord on restart or on boot.

## Using the pi from your personal computer

### Setup

- Download [VNC Viewer](https://www.realvnc.com/en/connect/download/viewer/)
- Make sure your computer can or has an app to allow you to ssh to a device.

### Usage

- For normal SSH access run `ssh pi@0.tcp.ngrok.io -p PORTNUMBERHERE` and your done!
- For VNC and SSH access run `ssh -L 5900:localhost:5900 pi@0.tcp.ngrok.io -p PORTNUMBERHERE`.
- Connect to localhost:5900 in VNC Viewer to VNC to the pi.
