# Pre-Requisite
* Raspberry Pi can ONLY be accessed from lab's network.
* They are automatically configured to connect the the below WiFi
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

---

# OS Install
We use [HyPriot OS](https://blog.hypriot.com/), a lightwieght Ubuntu-based, for our PIs, unless said otherwise. It does not have a GUI, but it will always derive the HDMI output (even if you did not connect it during boot). 

For making an SD card, you can use their [`flash`](https://github.com/hypriot/flash) tool. Below is an example:

```
flash -u cloud-config.yml https://github.com/hypriot/image-builder-rpi/releases/download/v1.11.0/hypriotos-rpi-v1.11.0.img.zip
```

and the content for `cloud-config.yml` to automatically configure user, password, and WiFi connection:
```
#cloud-config
# vim: syntax=yaml
#

# The current version of cloud-init in the Hypriot rpi-64 is 0.7.9
# When dealing with cloud-init, it is SUPER important to know the version
# I have wasted many hours creating servers to find out the module I was trying to use wasn't in the cloud-init version I had
# Documentation: http://cloudinit.readthedocs.io/en/0.7.9/index.html

# Set your hostname here, the manage_etc_hosts will update the hosts file entries as well
hostname: pi
manage_etc_hosts: true

# You could modify this for your own user information
users:
  - name: pi
    gecos: "Hypriot Pirate"
    sudo: ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash
    groups: users,docker,video
    plain_text_passwd: raspberry
    lock_passwd: false
    ssh_pwauth: true
    chpasswd: { expire: false }

# # Set the locale of the system
# locale: "en_US.UTF-8"

# # Set the timezone
# # Value of 'timezone' must exist in /usr/share/zoneinfo
# timezone: "America/Los_Angeles"

# # Update apt packages on first boot
# package_update: true
# package_upgrade: true
# package_reboot_if_required: true
package_upgrade: false

# # Install any additional apt packages you need here
# packages:
#  - ntp

# # WiFi connect to HotSpot
# To make wifi work with RPi3 and RPi0
# you also have to set "enable_uart=0" in config.txt
# See no-uart-config.txt for an example.
#
# # - use `wpa_passphrase SSID PASSWORD` to encrypt the psk
write_files:
  - content: |
      allow-hotplug wlan0
      iface wlan0 inet dhcp
      wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
      iface default inet dhcp
    path: /etc/network/interfaces.d/wlan0
  - content: |
      country=US
      ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
      update_config=1
      network={
      ssid="NETGEAR79"
      psk="78zBJr!4bVdpaFIQ"
      proto=RSN
      key_mgmt=WPA-PSK
      pairwise=CCMP
      auth_alg=OPEN
      }
    path: /etc/wpa_supplicant/wpa_supplicant.conf

# These commands will be ran once on first boot only
runcmd:
  # Pickup the hostname changes
  - 'systemctl restart avahi-daemon'

  # Activate WiFi interface
  - 'ifup wlan0'
```


---

# Connect to Wifi with Terminal
edit the file `/etc/wpa_supplicant/wpa_supplicant.conf` with `sudo nano /etc/wpa_supplicant/wpa_supplicant.conf`. Copy below info:

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US

network={
        ssid="NETGEAR79"
        psk="78zBJr!4bVdpaFIQ"
        proto=RSN
        key_mgmt=WPA-PSK
        pairwise=CCMP
        auth_alg=OPEN
}
```


Exit with ctrl^x, Enter.
