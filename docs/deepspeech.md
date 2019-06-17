# Utilizing Deepspeech on Raspberry pi 

**Requirements: have python3 installed with pip3**

https://github.com/mozilla/DeepSpeech#using-the-python-package


# Run deepspeech with training model: (use python deepspeech package) 
**WARNING: this model is really big: 1.6 GB; so you cannot do this on raspberry pi**

Follow steps under Using Pre-trained mode on the github page (https://github.com/mozilla/DeepSpeech#using-the-python-package), using python package which are:

**Make a virtual environment:**

- Pip3 install virtualenv if you don’t have virtualenv python package yet (or pip) version

```
virtualenv -p python3 $HOME/tmp/deepspeech-venv/
```

- Instead of $HOME/tmp/deepspeech-venv, put the path of where you want the virtual environment to be made
- deepspeech-venv will be the name of the environment so change that if you want a different name
- Or just make a virtualenv how you normally do

**Activate the virtual environment**

- Now the virtual environment is created with a bin folder with activate document

`source $HOME/tmp/deepspeech-venv/bin/activate`

- This creates a virtual environment where you can install deepspeech related dependencies
- Now install deepspeech package on your local environment

`pip3 install deepspeech`

**Using this: https://github.com/mozilla/DeepSpeech#getting-the-pre-trained-model, download the latest pre-trained deepspeech model: (You can use an older one if you want to)**

- Linux: run this command in the directory you want to put the file: 
`wget https://github.com/mozilla/DeepSpeech/releases/download/v0.5.0/deepspeech-0.5.0-models.tar.gz`

- Others, just enter link into web browser, this will download the file. Then manually move the file to preferred directory
- Then, unzip the file using tar command 
`tar xvfz deepspeech-0.5.0-models.tar.gz `

- This creates a folder, called deepspeech-0.5.0-models
- Now download an audio file you want the model to do speech to text recognition
- Put this model in the preferred directory
- Go to the preferred directory on the command line and run this command:
`deepspeech --model models/output_graph.pbmm --alphabet models/alphabet.txt --lm models/lm.binary --trie models/trie --audio my_audio_file.wav`

- EXCEPT: replace my_audio_file.wav with your audio file and 
--lm and --trie tags are optional
- Replace models with deepspeech-0.5.0-models or with the name of the folder created from the download




# Next we tried to make our own model to see if we can reduce the model size:
- Using steps from https://github.com/mozilla/DeepSpeech#training-your-own-model:
- Make or activate your virtualenv for deepspeech
- Git clone DeepSpeech from the github 
 `git clone https://github.com/mozilla/DeepSpeech.git`
- Install required dependencies from requirements.txt file, Run these commands
` cd deepspeech `
`pip3 install -r requirements.txt`
- If you are using gpu, use tensorflow gpu:
`pip3 uninstall tensorflow`
`pip3 install 'tensorflow-gpu==1.13.1' `
- Download voice training data from common voice: https://voice.mozilla.org/en/datasets; _ - Download the Tatoeba dataset
- Go to the link, scroll down to the Tatoeba dataset, press more, and press download
- Move it to your preferrred directory
- Unzip the file 
- Convert MP3 to WAV file while creating train.csv, dev.csv, and test.csv
- Put ‘import.py’ and ‘untilA.csv’ in same folder
- Install pydub (pydub will help convert MP3 to WAV)
- pip3 install pydub
- (Optional) apt-get install ffmpeg
- Edit import.py before you start running the code
- Change the fullpath variable to the directory that has the audio files
- For example, fullpath = ‘/home/user/Download/tatoeba_audio_eng/tatoeba_audio_eng/audio’
- Now, run import.py by
- python3 import.py
- As a result, you will have the following files:
new_names.csv
train.csv
dev.csv
test.csv
**‘new_names.csv’ is just a file that contains all wav file directories**
- Using ./Deepspeech.py to create your own model
`./DeepSpeech.py --train_files /locate/directory/here/train.csv --dev_files /locate/directory/here/dev.csv --test_files /locate/directory/here/test.csv`
