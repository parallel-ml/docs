# Building the Raspberry Pi Drone

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


## Installation
* Clone or Download the above git repository
* cd to __BreezySLAM/python__ and run the following command
  * `$ sudo python3 setup.py install`    
* For installing the rplidar python package run the following pip command
  * ` $ sudo pip3 install rplidar`
* For seeing the live animation of the map, we install PyRoboViz
    * cd to __PyRoboViz__ folder and run the following command
      `sudo python3 setup.py install `

## Running Slam
* Connect the lidar to the system via a USB port and run the following command in the terminal
`sudo chmod 666 /dev/ttyUSB0 `
* From the **BreezySLAM/examples** folder run the rpslam .py file to get live map of the surrounding.
