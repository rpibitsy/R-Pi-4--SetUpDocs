# R-Pi-4--SetUpDocs

## Table of Contents

- [WriteR Pi OS Image to a Device](#write-image-to-a-device)
- [Install Suggested Additional Apps](#install-suggested-additional-apps)
- [Install MQTTX for testing MQTT](#install-mqttx)
- [Using Tmux](#using-tmux)
- [Install Docker](#install-docker)
- [Install Portainer](#install-portainer)
- [Add Docker Compose](#add-docker-compose)
- [Add Home Assistant Container](#add-home-assistant-container)
- [HomeAssistant Supervised on Docke](#homeassistant-supervised-on-docker)
- [Make The R Pi 4 host be "homeassistant"](#set-pi-host-to-be-homeassistant)

## Write Image to a device.

# Choose what to run your OS on.  

Suggested choices are (with search terms "in quotes")

-A large fast SD card like: "SanDisk 64GB Extreme PRO microSDXC card".
-A fast USB drive e.g. "SanDisk 128GB Ultra Fit USB 3.1 Flash Drive"
-An M.2 SSD and enclosre e.g. "ORICO-NVMe-Enclosure", "256GB NVMe SSD M.2 2280 Internal Solid State Drive"

# Write the Raspberry Pi 64bit OS.

For detailed instructions and explanations see

[Raspberry Pi Website: Raspberry Pi OS](https://www.raspberrypi.com/software/)

Here is suggested method and choices etc:

Download to your PC the Raspberry pi Imager it is by far the best way to write Operting System images to an SD card or to USB or SSD or M.2  SSD.

Download the imager using this link and install and run  [Downloads/Imager-Latest](https://downloads.raspberrypi.org/imager/imager_latest.exe)

Procedure:
-Run the imager.
-Click on "Raspberry Pi OS (other)
-Click on "Raspberry Pi OS (64-bit)
-Click on the Gear Icon on the Imager icon and enter details including setting hostname to "homeassistant", Wi Fi details, password etc.
-Click on "Choose Storage" and select you device 
-and then Click "Write".

Once write completed and verified insert the SD card or USB into the Pi and power up.  If you correctly entered the wi fo#i detals the pi should appear on you local network (check on you router or using the Android "Fing" app) as "homeassistant".   Then you con connect using an app like Termius or using the windows "Terminal(Admin)" and entering "ssh homeassistant" or "ssh <your pi's IP>.

Then to use VNC, VNC needs to be enabled by entering 

```
raspi-config
```

and turning on VNC in the Interfaces list.

Then you can connect by VNC using the RealVNC Viwere downloaded from [Real VNC Viwer Download](https://www.realvnc.com/en/connect/download/viewer/)

However it much easier just to conect Keyboard Mouse and monitor, at least to start with.

In the evn of any issue getting connected over Wi Fi best way is conect to the router direct with a netwok cable.

### Install Suggested Additional Apps

Tmux for running Python code see [Tmux wiki](https://github.com/tmux/tmux/wiki)  
```
sudo apt-get install tmu
```

Drive Partition editor:

```
sudo apt install gparted
```

Firefox browser

```
sudo apt install firefox-esr
```

Libre Office:

```
sudo apt install libreoffice
```

Visual Code editor

```
sudo apt install code
```

Any required Python Modules such as the one for reading from temperature sensor

```
pip3 install w1thermsensor
```

Install nginx web server.  This sets up a local web server which allows status to be viewed in local network.  My Python code generates HTML files which get copied to /var/www/html/. Install using
```
sudo apt install nginx
```

Then make the folder where the html files will be put writeable using
```
sudo chown -R pi /var/www/html/
```
## Install MQTTX

This can only be installed as an App Image.

Get the appimage from [MQTTX Github](https://github.com/emqx/MQTTX/releases)

Put in an appropriate folder and make executable using:

```
chmod u+x MQTTX-1.9.1-arm64.AppImage
```
(after changing t the folder where it was put)

Then add to the menu using the "Menu Editor" found under "Preferences".

### Using Tmux

The way I use "tmux" there are several ways of starting python code
```
cd /home/pi/<code folder>
python3 <appcode>.py
```  
To run code in a tmux session and view its output 

```
cd /home/pi/<code folder>
./tmux_start.sh
tmux a
```

Where "tmux start.sh" is a script in the code folder containing something like this example for the shedheat.py prog.

```
#!/bin/bash
cd /home/pi/shedHeat
echo looking to kill any old tmux tank session
tmux kill-session -t shedHeat
echo now new tmux shedHeat session 
tmux new-session -d -s shedHeat 'python3 shedHeatHp.py'
echo tmux session has been started    Press Enter 
exit 0
```

Set up script to run automatically at reboot by editing rc.local. To do this with the nano editor do: 
```
sudo nano /etc/rc.local
```
        
add before the "exit 0" at the end of the file add  (replaceing "shedheat" with te appropriate folder     
```
sudo -u pi bash /home/pi/shedheat/tmux_start.sh &
```
        
then check rc.local works by doing
``` 
cd /etc
./rc.local
```
        
then when restart Pi the python program should start automatically.
Code runs in a tmux session and you can view it using 
```
tmux a -t shedheat
```
        
or just this if only one tmux session in use
 ```
tmux a
 ```
## Install Docker

For detailed information and explanation  see 
[Pi My Life Up : Raspberry Pi Docker](https://pimylifeup.com/raspberry-pi-docker/)

Here are the commands he suggests:

First make sure up to date:
```
sudo apt update
sudo apt upgrade
```

Then

```
curl -sSL https://get.docker.com | sh
```

Then add the the "pi" user to the "docker" group with

```
sudo usermod -aG docker pi
```

then rboot

```
reboot
```

and check the group change worked with

```
groups
```

Docker should be in the list.

Then check docker installed OK using the "Hullo World Container"

```
docker run hello-world
```

### Set Pi hostname  To Be "homeassistant"

On pi select menu Preferences/Raspberry Pi Configuration.

Then select System/Change Hostname

Then enter "homeassistant".

Then click "OK" twice and reboot.

Now to view the Pis web site you can just use {http://homeassistant.local:8123) or {http://homeassistant.local:9000) or {http://homeassistant.local) depending whether wanting Home Assistant, Portainer or its nginx website.

## Install Portainer

For detailed explanationms and instructions see  

[Pi My Life Up :: Raspberry Pi Portainer](https://pimylifeup.com/raspberry-pi-portainer/)

Add the portainer as a docker container with

```
sudo docker pull portainer/portainer-ce:latest
```

Tell Docker to run this container using  ....
```
sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

now connect with   (http://[PIIPADDRESS]:9000)  or if you have used [Set Pi Host to be homeassistant](#set-pi-host-to-be-homeassistant) below you can conect with (http://homeassistant.local:9000).  You could also use  http://localhost:9000/ if you are using the pis own browser.

now assign user name and password and start detailked set up of required functioningality. 

## Add Docker Compose

add docker compose with

```
sudo apt-get install docker-compose
```

## Add Home Assistant Container

For detailed info and explanations see 
[Home Assistant / Raspberry Pi / Container](https://www.home-assistant.io/installation/raspberrypi#install-home-assistant-container)

They sugeested adding with

```
docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=MY_TIME_ZONE \
  -v /PATH_TO_YOUR_CONFIG:/config \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
 ```
  
which it is suggested should be changed to this not the config is copied to a directory in .config as pi uswer can edit that.
some sites suggest put in /opt but thats tricky for editing

```
docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -p 8123:8123 \
  -e TZ="Europe/London" \
  -v /home/pi/.config/homeassistant/config:/config \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
````

 Then, simmilar to Portainer, connect using (http://homeassistant.local:8123) or (http://localhost:8123) if using the browser on the pi itself.
 
 
 ## HomeAssistant Supervised on Docker
 
 This You tube Video Describes a method of installing the Home Assistant Supervisor as a docker container.
 
 I have NOT tried this and do not intend to.  However this may have advantages for some people cokpared to using Portainer to organise additional facilities as the supervisor would do all the integration of the needed functions between containjers.
 
 [You Tube: HomeAssistant Supervised on Docker](https://youtu.be/HPYK0uVj4Z8)
  
## Set Pi Host to be homeassistant

On pi select menu Preferences/Raspberry Pi Configuration.

Then select System/Change Hostname

Then enter "homeassistant".

Then click "OK" twice and reboot.

Now to view Portainer, Home Assistant or the Pis web site you can just use {http://homeassistant.local:8123) or {http://homeassistant.local:9000) or {http://homeassistant.local).
