<h2>Fitness Activity Monitor Setup</h2>

1) Download the operating system image  
https://www.raspberrypi.org/downloads/raspbian/  

2) Download Etcher for flashing the operating system to a micro SD card  
https://etcher.io/  

3) Now flash the downloaded OS image onto the SD card using the Etcher tool  

4) Upon Raspberry Pi boot run,  
<code>sudo raspi-config</code> (Run the wifi script If needed and/or enable SSH)

5) Type in, <code>sudo ip link show</code>, this will produce your network interfaces,  
ensure that wlan0 or eth0 for are marked as 'UP'  

6) At this point you can run a good ol' ping test  
<code>ping google.com</code>  

7) Now run the following commands to install needed packages  
<code> sudo apt-get update</code>  
<code>sudo apt-get upgrade</code>  
<code>sudo apt-get vim  </code>  
<code>sudo apt-get install python-bluez</code>  
<code> sudo apt-get install</code>  

8) Set up the keyboard to the U.S. standard  
<code>vim /etc/default/keyboard</code>  
Change the "gb" to "us", save the file and upon reboot it will be changed  

9) Insert the Bluetooth code, first you will need the MAC address of the server device. To find this use,  
<code>hciconfig -a</code>  
MAC address format looks like this "XX:XX:XX:XX:XX:XX" 

10) Now with the given MAC address we can start adding code for our bluetooth client and server.  
For each device add the following files while changing the specified MAC address for BOTH, to the  
server MAC address we got from the previous step. Links to both files provided below.  
https://github.com/dwbell/FitnessMonitor/tree/master/Pi_Bluetooth

11) Install a package called 'supervisor' which is a Linux based daemon manager. This package will ensure  
that a script is continuously ran regardless of any errors that may occur from prolonged execution.  
<code>sudo apt-get install supervisor</code>  

12) Once 'supervisor' is installed you need to create the configuration script  
<code> sudo su </code>  
<code> echo supervisord_conf > /etc/supervisord.conf</code>  

13) With a default config file in place, we need to add a program to be watched under supervisor.  
To accomplish this we add a [program] block to /etc/supervisord.conf  

```[program:bluetooth]
directory:/home/pi
command=/usr/bin/python /home/pi/bluetoothserver.py
autostart=true
autorestart=true
startsecs=0
startretries=3
log_stdout=true
log_stderr=true
stderr_logfile=/home/pi/test.err.log
stdout_logfile=/home/pi/test.out.log
```
