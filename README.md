# R-Pi-4--SetUpDocs

## Table of Contents
- [Install Suggested Additional Apps](#install-suggested-additional=apps)
- [Install MQTTX for testing MQTT](#install-mqtxx)
- [Using Tmux](#using-tmux)
- [Install Docker](#install-docker)
- [Install Portainer](#install-portainer)
- [Add Docker Compose](#add-docker-compose)
- [Add Home Assistant Container](#add-home-assistant-container)

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
### Install MQTTX for testing MQTT

Get the appimage from [MQTTX Github](https://github.com/emqx/MQTTX/releases)

Put in an appropriate folder and make executable using:

```
chmod u+x MQTTX-1.9.1-arm64.AppImage
```
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

install docker as per .....
https://pimylifeup.com/raspberry-pi-docker/

sudo apt update
sudo apt upgrade

curl -sSL https://get.docker.com | sh
docker run hello-world


reboot
groups

(check docker in list of groups)

simple check


docker run hello-world

## Install Portainer

install portainer as per 

https://pimylifeup.com/raspberry-pi-portainer/

download latest portainer iage with 

sudo docker pull portainer/portainer-ce:latest

Telling Docker to run this container ....

sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

now connect with   http://[PIIPADDRESS]:9000

or on pi with http://localhost:9000/

now assign user name :  rpibitsy

and used password:    bitsybonerpi

## Add Docker Compose

add docker compose with

sudo apt-get install docker-compose

## Add Home Assistant Container

followed Homeassistant container instructions from 
https://www.homeassistant.io/installation/raspberrypi


docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=MY_TIME_ZONE \
  -v /PATH_TO_YOUR_CONFIG:/config \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
  
chasnged to

docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=GB \
  -v /opt:/config \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
