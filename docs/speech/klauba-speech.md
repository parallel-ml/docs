# Klauba Speech
Author: Bryce Plunkett

# Introduction
In this article, I will go over how to get Klauba's (current) iteration of speech recongition code to run on the Raspberry PI or an Ubuntu-based machine. 

**BEFORE** you read this article, I encourage you to check out the *Sphinx* doc. All of the code/scripts are iterations and improvements of that code.

# Major Changes
* Implemented a hotword detector (Listens for "Klauba") before recording and parsing speech input
* "Intelligent" voice recording after "Klauba" is detected
    * Wont' start recording until silence has been broken
    * Stops recording after a certain amount of continous silence
* Improved accuracy of speech recognition
    * Fine-tuned the corpus (and regenerated language and lexical models)
    * Created a script to autogenerate corpuses based off of sententence templates and room numbers
    * Removed the pre-input noise filtering code (in the previous iteration, we noise filtered the code BEFORE we passed it to shpinx)
* Parsed speech now passed to a python script, which can distribute the text to all other code (like the movement code)
* Script won't stop running until stopped by the user

# Installing Dependencies and Code Base
**NOTE:** This can be a little tricky. Every system is different and requires a slightly different install process. If you run into any problems, feel free to shoot me an e-mail: `plunkett.bryce.m@gatech.edu`.

*Snowboy* refers to the library used for hotword detection
*Sphinx (Pocketsphinx, CMUSphinx)* refers to the library used for speech recognition

## Supported Operating Systems and Prerequisites:
This OS list has been pulled from Snowboy git docs. The operating systems marked with a "*",  have tested the code on 

* all versions of Raspberry Pi (with Raspbian based on Debian Jessie 8.0)
* 64bit Ubuntu 14.04*
* 64bit Mac OS X
* ARM64 (aarch64, Ubuntu 16.04)*

You also need to have **Python 2.7** and **git** installed.

**Ultimately, this guide assumes you are on an Ubuntu-based operating system**

## Configuring your microphone
You might need to redo this step whenever a USB device with a microphone (including webcams) is plugged into the machine you're running the code on.


**To Test if your microphone is configured correctly:** 
` rec {{file name}}.wav`
Hit `contrl-c` when you're done recording. If you hear your voice in the generated `.wav`, then your microphone is configured correctly. If not, then your microphone is not configured correctly. 

**If your microphone is not configured correctly:**

Edit (or create if it does not exist): `/etc/asound.conf`

Copy and paste into the file and edit the *playback* card and *record* card. You will need to do this as the *root user*.

To get playback devices: `aplay -l`  
To get recording devices: `arecord -l`
```
pcm.!default {
  type asym
   playback.pcm {
     type plug
     slave.pcm "hw {{playback card #}}, {{playback device #}}"
   }
   capture.pcm {
     type plug
     slave.pcm "hw {{record card #}}, {{record device #}}"
   }
}
```
Example */etc/asound.conf*: 
```
pcm.!default {
  type asym
   playback.pcm {
     type plug
     slave.pcm "hw:0,0"
   }
   capture.pcm {
     type plug
     slave.pcm "hw:1,0"
   }
}
```
Test your changes with the `arec` command

## Installing Snowboy
First, you need to install Snowboy (the library used for hotword detection). This tends to always pose some complications.

You can try following the guide below or the [official Snowboy guide](https://github.com/Kitt-AI/snowboy). I recommend trying to follow the Snowboy guide and using this guide as a reference.

### Installing Swig
You need a version of swig installed **>= 3.0.10**. I have tried older versions with no success. Snowboy will refuse to completely build. Unfortunately, the newer version of swig does not seem to be on Ubuntu's apt repository, so you will need to manually build and install it.

Here are several resources to help you install swig:
* http://www.linuxfromscratch.org/blfs/view/7.10/general/swig.html
* https://github.com/swig/swig/blob/master/INSTALL
* https://github.com/Kitt-AI/snowboy (scroll to the section that details installing swig)

**Verify your swig install**: `swig -version`

### Install other snowboy dependencies
```
$ sudo apt-get install python-pyaudio sox
$ pip install pyaudio
$ sudo apt-get install libatlas-base-dev
```

### Compiling Python wrapper 
Clone the Snowboy repository
```
git clone https://github.com/Kitt-AI/snowboy.git
cd snowboy
```

Compile the Python wrapper (assuming you're cd'ed into the snowboy root directory)
```
cd swig/Python
make
```

### Setting Environment Variable
Ideally, you successfully compiled the Snowboy python wrapper. Now, our python needs a way to access what you compiled. 

You need to add `{{Snowboy Root Directory}}/examples/Python` to the `PYTHONPATH` environment variable. Whenever you run a command in python, one place it will search for depencies are all the directories listed in the `PYTHONPATH`. 

Replace *{{Snowboy Root Directory}}* with an absolute path to your Snowboy code. 

To test you set this environment variable correctly, open up a new terminal and type: `echo $PYTHONPATH`. Your path to the Python Snowboy wrapper should now be in list of variables in the PYTHONPATH. 


## Installing Our Code
### Clone and install dependencies:
```
$ git clone https://github.com/parallel-ml/sphinxSpeech2Text
$ cd ./sphinxSpeech2Text
$ sudo chmod u+x ./install.sh
$ ./install.sh
```

### Compile
```
$ make
```

### Before you can run the code:
- Set the `SPEECH_RECOGNITION` environment variable to point to the repository. For instance, if you cloned the repository into `~`, then
the `SPEECH_RECOGNITION` environment variable should be set to `~/sphinxSpeech2Text`. 
- DO NOT USE any shorthand such as `~` in the environment variable

# Running Code For the First Time
To run the code, call `./snowboy-code` . All processed output will be saved to 

## Fixing errors
You will probably see many errors related to audio dependencies on your machine. This [issue thread](https://github.com/Kitt-AI/snowboy/issues/9#issuecomment-241577381) on the Snowboy Github repository does a great job solving most of them. Please refer to it **FIRST** before continuing with this guide

### Jack Control X11 Issue
If `jack_control start` throws some issue about X11 adapter, it's because you're SSHing into (presumably a raspberry pi) without X11 forwarding (don't ask me why jack_control wants a display). 

To fix this issue, when you SSH into the machine you're installing the code on, use the "-X" (case sensitive) parameter: `ssh -X {{User name}}@{{IP}}`.

### Cannot lock down byte memory
The error regarding "byte memory" appears to not affect how the code runs. It can be ignored (it's a glitch with snowboy)


### The console hangs/freezes when "Klauba" is detected
If the console hangs after detecting Klauba, in other words, nothing happens after ```INFO:snowboy:Keyword 1 detected at time: {{DATE} {{TIME}}```:

This is a bizarre glitch with Snowboy. Luckily, there is a fix...   
1. Hit `control-z` (exits the script)
2. Edit `klauba.py` and comment line #12  out (`detector.terminate()`) (**On the Raspberry pis, this line NEEDS to run. It should not be commented out on the Raspberry PIs**)
3. Restart your machine


# Code Explanation
**All paths in the following explanation will treat the repository as the root directory**

`/snowboy-decode.c`: This is the main script. It calls all python "helper" scripts and also make the call to the pocketshpinx library to decode the data. It runs infinitely until the user kills the script with a process signal. **You can change the lexical model (pronunciation) dictionary and language model passed to Klauba in this code at lines #21 and #22, respectively.**

`/klauba.py`: This is the hotword detection script. It uses the Snowboy python wrapper. If it detects the "Klauba" hotword, it calls the `/record.py` script

`/record.py`: This script won't start recording until a break in silence is detected. Once it starts recording, it won't stop recording until a certain amount of configured silence. This idea of continous silence follows the "leaky bucket" design pattern where the water is silence. Silence fills the bucket, while noise empties the bucket. If the bucket overflows, then the recording stops and it will be saved to `/inputs/raw_recording.wav`. Once this script ends, `snowboy-decode.c` will call the **pocketsphinx library to decode the recording.**

`/output/decode-details.log`: A log file generated by pocketsphinx decodes the audio into text. If there any errors with your pocketsphinx install or the parameters (such as the models) you pass in, they will be logged here.

`/output/log.txt`: A timestamped log of the words pocketsphinx decodes. It is generated by the `/process-output.py` script

`/process-output.py`: The output of pocketsphinx is passed to this script via a pipe in a bash command (standard input).  The `process_speech_input` function receives this input as a string. **Modify this function as necessary to handle what you want to do with the user's parsed speech input**

# Models
`/models/Klauba.pmdl`: This is the model that Snowboy uses for detecting the `Klauba` hotword. The current model can be downloaded/retrained at [this link](https://snowboy.kitt.ai/hotword/43141)

`/models/corpus/corpus-generator.py`: This script will autogenerate a corpus (`/models/corpus/corpus.txt`) for you based on:
* Template strings (encoded within the script itself) that take in a room numbers
* A list of general phrases `/models/corpus/general-phrases.txt` (no room numbers in these phrases)
* A list of room numbers (that are inserted into the templates) `/models/corpus/rooms.txt`

`models/corpus/Corpus_V3`: This directory contains the dictionary and language models currently being used by sphinx in the script. 

## Updating the Dictionary and Language Models
Once you have a corpus you want to use (*see /models/corpus/corpus-generator.py*):
1. Use [this website](http://www.speech.cs.cmu.edu/tools/lmtool-new.html) to create a dictionary and language model based on an inputted corpus
2. Download the `{{name}}.lm` and `{{name}}.dic` and save them to a directory. Recommended directory to save the new models to: `/models/corpus/Corpus_V{{corpus version number}}. **Be sure to include the corpus.txt in this directory to keep track of what the models were trained with!**
3. Edit `/snowboy-decode.c` and update line #21 with the new language model path and line #22 with the new dictionary model path
4. Don't forget to rebuid the c script. Call `make`.

# Future Improvements
Currently, the `/record.py` script cuts off the beginnning of user input (it's not recorded). As a result, sometimes the sphinx doesn't parse the first words of what the user says. For instance, the user will say, "Take me to room 2443" and sphinx wil parse it as "2443" or "ROOM 2443".