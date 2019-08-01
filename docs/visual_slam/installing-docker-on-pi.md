# Installing Docker on the Raspberry Pi
This document goes through the installation prcess for docker and coker-compose on the Raspberry Pi.

## Preparing Enironment
Run the following command in your terminal:

`sudo apt-get install -y build-essential libssl-dev libffi-dev`

## Installing Docker
Run the following command in your terminal:

`curl -sSL https://get.docker.com | sudo sh && sudo usermod -aG docker pi`

## Installing docker-compose
Run the following command in your terminal:

`sudo apt-get -y install python-setuptools && sudo easy_install pip && sudo pip install docker-compose`

## Other resources
- https://howchoo.com/g/nmrlzmq1ymn/how-to-install-docker-on-your-raspberry-pi
- https://stevenbreuls.com/2019/01/install-docker-on-raspberry-pi/
