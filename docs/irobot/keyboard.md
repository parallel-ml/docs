## Control with Keyboard
Make sure Pi is connected to power and serial  

```bash
ssh -X pi@192.168.1.2 
sudo chmod o+rw /dev/ttyUSB0  
gtkterm
```

Configure port to USB0  
Change baud rate to 115200  

```bash
python create2_cmds.py  
```

Click connect and type /dev/ttyUSB0 (the above)  
Press 'p' then 'f'  
The robot is now controllable  

To see data from sensors (power):
 - View -> Hexadecimal  
 - Log -> to somelogfile.txt  
 - Go to python and press 'z' to begin log stream  
 - Do whatever (ML stuff)  
 - Stop logging from log menu when done  
 - translatorStream.py -> change file read name to somelogfile.txt (whatever name)  
 - python translatorStream.py  
 - done.txt contains voltage and current values  

