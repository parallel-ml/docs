# Pre-Requisite
* Raspberry Pi can ONLY be accessed from lab's network.
* Connect to `NETGEAR79` Wi-Fi with password `78zBJr!4bVdpaFIQ`.

---

# Connect to Raspberry Pi
**Find IP of available Pi**  
Go to `192.168.1.1` in your browser with username `admin` and password `password` to check IP of all connected 
Raspberry Pis. Under `connected devices` tab, you will be able to see the IP of all connected Raspberry Pis.

**Connect through SSH**  
```text
ssh pi@<ip>
```

Use password `raspberry` for connection or for `sudo` command.

---

# Python Setup
**Python Environment**  
We are using Python 2.7 for this project. PLEASE DO NOT CHANGE THE RASPBERRY PI's DEFAULT PYTHON SETTING.

**Package Installation**  
`pip` is always recommended for Python package installations. A cleaner way would be using 
[virtualenv](https://virtualenv.pypa.io/en/latest/), so that your environment won't interfere with others'.

**Long compling time with pip installation**

You can use piwheels by placing the following lines in /etc/pip.conf:

```
[global]
extra-index-url=https://www.piwheels.org/simple
```

Then pip will search in wheels to install any package first.

Also, you can download your wheel from here manually:
https://pythonwheels.com/
