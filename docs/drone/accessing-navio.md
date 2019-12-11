# Accessing Navio and Executing code

## First connect to the NAVIO+PI
* `$ ssh pi@192.168.1.48`
* Password : raspberry
* You should now have SSHed into the Navio+Pi
* Note, the drone has already been configured to connect to the lab wifi network. Also, the router assigns the same IP **192.168.1.48** to the drone every time.

## Installing VENV and APIs
* `pip install dronekit`
* `sudo emlidtool --on_boot=True`
* `pip freeze | grep dronekit`
* Verify installation and version

## Running DroneKit and other code
* Please visit DroneKit docs on github to see what each function does.
* You can code in Python 3 or C++
* To run a Python file, `python test.py`
* To run a C++ file, first compile and then `./test`
* If everyhting is set up correctly, the drone will execute the commands given to it


## Monitoring with MissionPlanner
* Start up MissionPlanner
* Connect the other TELEM USB module to your computer
* Select `COM3` from top-right drop-down menu
* Click `Connect`
* Make sure drone is powered
* MissionPlanner should automatically connect to the drone and download all the variables and their types and start real-time monitoring
