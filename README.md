# R-Pi-4--SetUpDocs
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

