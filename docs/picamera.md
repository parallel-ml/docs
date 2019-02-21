## Using the PiCamera
For using the PiCamera on a Raspberry Pi, you should, after connecting the camera to the Raspberry, update and upgrade your pi with:

`sudo apt-get update`

and

`sudo apt-get upgrade`

That may take some short time.

Then, you have to enable the camera in the raspberry settings by:
'sudo raspi-config'

Reboot the pi after that:

`sudo reboot`

Give some time for the pi to reboot and log again into it.

Finally, test your camera with:

`raspistill -o image.jpg`

(it takes a picture and saves it as `image.jpg`)

After that, a new jpeg file should appear in your directory.

General Helpfull information about PiCamera (https://picamera.readthedocs.io/en/release-1.12)

## Visualizing pictures via ssh
If you want to display the images of image files in your computer, make sure to connect to the pi with:

`ssh -X pi@<pi_adress>`(focus on the `-X`)

Install `feh` if you the don't have it yet:

`sudo apt-get install feh`

And finally do:

`feh <image_file>.jpg`

