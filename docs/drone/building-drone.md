# Building And Configuring The Raspberry Pi Drone

## Requirements
* Hardware:
  * [Parts List](parts-list.md)
  * Router
  * 32 GB microSD card
* Software:
  * [Mission Planner](http://ardupilot.org/planner/docs/mission-planner-installation.html) [Needs **Windows** system or virtual machine]
  * Python 3
  * [Etcher](https://www.techspot.com/downloads/6931-etcher.html) [For **Windows**]
    * Or [Belina Etcher](https://www.balena.io/etcher/) [For **macOS**]

## Introduction
To build the drone, we need to gather several hardware components. Please refer to the [Parts List](parts-list.md) and ensure all parts are present before building.


## Build Guide
Note : Most instructions can be found in the manual located in the frame box.

* First assemble the PI + NAVIO. Plug in the HAT into the GPIO pins on the RPI.
* Solder the bullet connectors onto the motor connections
* Solder the battery connector onto the Power Distribution Board (PDB)
* Screw in the legs of the frame
* Screw in the top plate to the frame
* Attach motors to the frame according to the rotational direction listed in the motor manual
* Use double sided tape and attach Raspberry pi + NAVIO to drone top plate
* Use double sided tape and attach the PPM encoder to the frame.
* Connect the battery connectors to the PPM encoder.
* Stick the RC receiver onto the frame.
* Connect receiver to the NAVIO
* Connect PPM outputs to NAVIO
* Use zip ties and attach ESCs to the bottom of the legs
* Assemble the GPS mount and zip tie it into the back-right leg
(Note : GPS unit must point North-South)
* Attach GPS on mount and connect GPS to NAVIO
* Connect ESCs to motors and ESCs pwm to PPM encoder
* Connect battery to battery connector
* Finally connect TELEM module to HAT and stick module onto frame

## Hardware Calibration
* Calibrate ESCs using the RC controller and charged battery
* Calibrate barometer using known height
* Calibrate Compasses using the GPS signal

## Installation
* Download EMLID OS image from EMLID website (for Navio2)
* Use Etcher to burn image onto SD card
* Insert SD card into PI
* Configure the PI to connect to WIFI
* Download MissionPlanner on Windows machine or VM

## Accessing APIs and flying
Please visit [Accessing Navio](accessing-navio.md)
