# R-Pi-4--SetUpDocs

## Table of Contents

- [Install Suggested Additional Apps](#install-suggested-additional-apps)
- [Install MQTTX for testing MQTT](#install-mqttx)
- [Using Tmux](#using-tmux)
- [Install Docker](#install-docker)
- [Install Portainer](#install-portainer)
- [Add Docker Compose](#add-docker-compose)
- [Add Home Assistant Container](#add-home-assistant-container)
- [HomeAssistant Supervised on Docke](#homeassistant-supervised-on-docker)
- [Make The R Pi 4 host be "homeassistant"](#set-pi-to-be-"homeassistant"]

### Install Suggested Additional Apps

Tmux for running Python code see [Tmux wiki](https://github.com/tmux/tmux/wiki)  
```
sudo apt-get install tmux
```

Install nginx web server.  This sets up a local web server which allows status to be viewed in local network.  My Python code generates HTML files which get copied to /var/www/html/. Install using
```
sudo apt install nginx
```
Then make the folder where the html files will be put writeable using
```
sudo chown -R pi /var/www/html/
```
### Install MQTTX

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
'''
sudo apt update
sudo apt upgrade
'''

Then

'''
curl -sSL https://get.docker.com | sh
'''

Then add the the "pi" user to the "docker" group with

'''
sudo usermod -aG docker pi
'''

then rboot

'''
reboot
'''

and check the group change worked with

'''
groups
'''

Docker should be in the list.

Then check docker installed OK using the "Hullo World Container"

'''
docker run hello-world
'''

###Set Pi To Be "homeassistant"

On pi select menu Preferences/Raspberry Pi Configuration.

Then select System/Change Hostname

Then enter "homeassistant".

Then click "OK" twice and reboot.

Now to view the Pis web site you can just use {http://homeassistant.local:8123) or {http://homeassistant.local:9000) or {http://homeassistant.local) depending whether wanting Home Assistant, Portainer or its nginx website.

## Install Portainer

For detailed explanationms and instructions see  

[Pi My Life Up :: Raspberry Pi Portainer](https://pimylifeup.com/raspberry-pi-portainer/)

Add the portainer as a docker container with

'''
sudo docker pull portainer/portainer-ce:latest
'''

Tell Docker to run this container using  ....
'''
sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
'''

now connect with   (http://[PIIPADDRESS]:9000)  or if you have use [Make The R Pi 4 host be "homeassistant"](#set-pi-to-be-"homeassistant"] below conect with (http://homeassistant.local:9000).  You could also use  http://localhost:9000/..

now assign user name and password. 

## Add Docker Compose

add docker compose with

'''
sudo apt-get install docker-compose
'''

## Add Home Assistant Container

For detailed info and explanations see 
[Home Assistant / Raspberry Pi / Container](https://www.home-assistant.io/installation/raspberrypi#install-home-assistant-container)

They sugeested adding with

'''
docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=MY_TIME_ZONE \
  -v /PATH_TO_YOUR_CONFIG:/config \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
 ''' 
  
which it is suggested should be changed to 

'''
docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=GB \
  -v /opt:/config \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
  '''
 
 Then, simmilar to Portainer, connect using (http://homeassistant.local:8123) or (http://localhost:8123) if using the browser on the pi itself.
 
 
 ## HomeAssistant Supervised on Docker
 
 This You tube Video Describes a method of installing the Home Assistant Supervisor as a docker container.
 
 I have NOT tried this and do not intend to.  However this may have advantages for some people cokpared to using Portainer to organise additional facilities as the supervisor would do all the integration of the needed functions between containjers.
 
 [You Tube: HomeAssistant Supervised on Docker](https://youtu.be/HPYK0uVj4Z8)
  
###Set Pi To Be "homeassistant"

On pi select menu Preferences/Raspberry Pi Configuration.

Then select System/Change Hostname

Then enter "homeassistant".

Then click "OK" twice and reboot.

Now to view the Pis web site you can just use {http://homeassistant.local:8123) or {http://homeassistant.local:9000) or {http://homeassistant.local) depending whether wanting Home Assistant, Portainer or its nginx website.
