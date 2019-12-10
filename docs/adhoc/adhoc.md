# Creating an ad hoc node with a Raspberry Pi
Create a script for setting up the interfaces on boot  
`cd ~ && touch start-batman-adv.sh && chmod +x start-batman-adv.sh`  
Then:  
`sudo nano start-batman-adv.sh`

And write this in the file:
```
#!/bin/bash
# Tell batman-adv which interface to use
sudo batctl if add wlan0
# Activates the interfaces for batman-adv
sudo ifconfig wlan0 up
sudo ifconfig bat0 up # bat0 is created via the first command 
```
Now, create two files in /etc/network/interfaces.d called bat0 and wlan0:  
`cd /etc/network/interfaces.d`

Backup your current wlan0:  
`sudo cp wlan0 wlan0.old`

Then write in the wlan0 file:  
`sudo nano wlan0`

And put this:
```
auto wlan0
iface wlan0 inet manual
   mtu 1532
   wireless-channel 3
   wireless-essid meshPi
   wireless-mode ad-hoc
   wireless-ap 02:12:34:56:78:9A
```
Then create and write the bat0 file:  
`sudo nano bat0`

And write:
```
auto bat0
iface bat0 inet auto
   pre-up /usr/sbin/batctl if add wlan0
```

Now, install batman-adv:  
`sudo apt-get install -y batctl`

And do the following: 
Have batman-adv startup automatically on boot:  
`echo 'batman-adv' | sudo tee --append /etc/modules`  
Prevent DHCPCD from automatically configuring wlan0:  
`echo 'denyinterfaces wlan0' | sudo tee --append /etc/dhcpcd.conf`  
Now, go back to home:  
`cd`  
And enable interfaces on boot:  
`echo "$(pwd)/start-batman-adv.sh" >> ~/.bashrc`  

Now, reboot:  
`sudo reboot`

Test if it worked (you should see “wlan0: active”):  
`sudo batctl if`


Source:  
https://medium.com/@tdoll/how-to-setup-a-raspberry-pi-ad-hoc-network-using-batman-adv-on-raspbian-stretch-lite-dce6eb896687
