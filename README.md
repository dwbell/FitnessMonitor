## Fitness Activity Monitor Setup  

#### Device
1) Download the operating system image  
https://www.raspberrypi.org/downloads/raspbian/  

2) Download Etcher for flashing the operating system to a micro SD card  
https://etcher.io/  

3) Now flash the downloaded OS image onto the SD card using the Etcher tool  

4) Upon Raspberry Pi boot run,  
<code>sudo raspi-config</code> (Run the wifi script and/or enable SSH as needed)

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
  
#### Bluetooth

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

```
[program:bluetooth]
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
After any changes to the /etc/supervisord.conf file, run the following commands to save  
<code> supervisorctl read </code>  
<code> supervisorctl update </code>

14) Now ensure that the bluetooth module and the 'supervisor' program both get started on boot.  
Add the this to the end of the file, BUT before the exit 0 line
<code> sudo vim /etc/rc.local </code> 
```
sudo hciconfig hci0 piscan
supervisord &
```
  
#### IBM Watson

15) In order to connect to IBM Watson you will need to set up an account with them through there website.  
https://www.ibm.com/internet-of-things/spotlight/watson-iot-platform  

16) With an account set up, run through the process of setting up a 'device' endpoint from IBM's documentation.
https://console.bluemix.net/docs/services/IoT/getting-started.html#getting-started-with-iotp  

17) With the endpoint setup we can now start implementating the code to control transmission to the  
IBM cloud. Add the following script into its own directory by issuing the following commands.  
<code>mkdir /home/pi/IBM_Watson</code>  
<code>cd /home/pi/IBM_Watson</code>  
<code> vim deviceClient.py</code>  
Now we can copy the script into that file we have just created. The script can be found here,
https://github.com/dwbell/FitnessMonitor/tree/master/IBM_Connect  

18) Now we need to add the configuration file to the same directory at /home/pi. This configuration file
allows us to enter individual credentials for your specific connection. The credentials will be obtained 
from when you ran through the IBM documentation listed above. The script is located here. (.cfg)

19) BEFORE running the code we will need to obtain a package from python's package repository otherwise known as  
'pip'. These packages allows us to interact with IBM Watson's cloud service. To run this command ensure you are  
in the /home/pi directory.  
<code>py -m pip install requests</code>  
<code>py -m pip install ibmiotf</code>  

