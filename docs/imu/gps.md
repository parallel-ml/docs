# NEO 6M GPS

## Setup
Raspberry PI uses the UART interface as a serial console by default. We have to deactivate this first! Connect to the PI and run:

```
sudo raspi-config
```

Option 5, P6 (Serial), No, Yes

```
sudo reboot
``` 

Then, we need  to connect the GPS module to the Raspberry PI. There are only 4 wires (F to F), so it's a simple connection. Use https://pinout.xyz/ if you don't know the numbering.

Neo-6M RPI:  
	- VCC to Pin 1, which is 3.3v
	- TX to Pin 10, which is RX (GPIO15)
	- RX to Pin 8, Which is TX (GPIO14)
	- Gnd to Pin 6, which is Gnd


```
sudo killall gpsd
sudo gpsd /dev/serial0 -F /var/run/gpsd.sock
sudo cat /dev/serial0
```

You should be able to see some messages:
```
$GPRMC,,V,,,,,,,,,,N*53
$GPVTG,,,,,,,,,N*30
$GPGGA,,,,,,0,00,99.99,,,,,,*48
$GPGSA,A,1,,,,,,,,,,,,,99.99,99.99,99.99*30
$GPGSV,1,1,01,02,,,20*78
```

## For Terminal Output
Then, run:
```
sudo gpsmon /dev/serial0
or
sudo cgps -s
```


References:  
	- https://www.xarg.org/2016/06/neo6mv2-gps-module-with-raspberry-pi/
  	- https://www.instructables.com/id/Raspberry-Pi-the-Neo-6M-GPS/
  	- https://www.raspberrypi.org/forums/viewtopic.php?t=180348
  	- https://raspberrypi.stackexchange.com/questions/88916/getting-nmea-unknown-msg58-and-other-garbage-from-my-neo-6m-0-001-gps-module
