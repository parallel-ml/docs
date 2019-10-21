# CMU Sphinx
Authors: Ramyad, Sayuj  
Date: 7/25/2019
# Set-Up (Fast)

**Prerequisites:**
- Python 2.7
- Numpy
- Ubuntu-Based OS (Ubuntu, PopOS...)

**Clone and install dependencies:**
```
$ git clone https://github.com/parallel-ml/sphinxSpeech2Text
$ cd ./sphinxSpeech2Text
$ sudo chmod u+x ./install.sh
$ ./install.sh
```

**Before compiling the C code:**
- You might need to edit line 12 and the `arecord` command parameters to match your environment (such as changing the recording device)


**Compile**:
```
$ make
```

**Before you can run the code:**
- Set the `SPEECH_RECOGNITION` environment variable to point to the repository. For instance, if you cloned the repository into `~`, then
the `SPEECH_RECOGNITION` environment variable should be set to `~/sphinxSpeech2Text`

**Running the code:**

You now have compiled the code. A demo can be run via
`./decode`. It will record your voice for 5 seconds, filter the noise, and try to parse commands from the limited corpus. The output text can be found in the `/output` folder and the raw and filtered recordings can be found in the `/testfiles` folder.
# Details
Parallel-ml repo: [https://github.com/parallel-ml/sphinxSpeech2Text](https://github.com/parallel-ml/sphinxSpeech2Text)

I used the pocketsphinx to decode the audio files on the raspberry pi’s. I installed it on the raspberry pi by following these instructions: [link](https://cmusphinx.github.io/wiki/tutorialpocketsphinx/#installation-on-unix-system)

Then I used the pocketsphinx_continuous command line command. There are multiple options, such as `-inmic`, which while use the system’s default microphone to detect and live decode the speech. You can also decode files using the `-infile` flag, then type the directory of the file relative to where you are calling the command from.

You can change the dictionary and the language model that the program uses by using the `-dict` and `-lm` flags. I created my own dictionary an language model using a tool I found online [link](http://www.speech.cs.cmu.edu/tools/lmtool-new.html), specifically made for pocketsphinx. I did this so that we could reduce the language model size to improve performance and accuracy. I found that the performance was 6x faster when I used my reduced dictionary, and obviously the accuracy is better, but it loses flexibility.

The next steps are to increase the dictionary to include a more variety of words, and increase the flexibility of commands that can be given to the raspberry pi. Below I have attached pictures of terminal output that shows the difference in performance. The output on the top shows performance with smaller dictionary and language model, the output on the bottom is the original dictionary that pocketsphinx comes with. It took more than 6x longer and it was less accurate.

```BASH
pi@n1:/Research$ ./decode.out  
MOVE DOWN  
MOVE UP  
TURN TO ME  
Time Elapsed: 2.049368  


pi@n1:/Research$ ./decode.out  
uh got caught  
move up  
learn to make  
Time Elapsed: 2.049368  
```


Originally it verbosely outputs every step while it processes the audio, and it was hard to find the actual output, so I created a command to output all the unwanted logs to a specific file, and the actual decoded speech into it’s own file.


### Example of Running in Terminal

```BASH
pocketsphinx_continuous -infile testfiles/Untitled.wav -dict dicts/8050.dic -lm dicts/8050.lm
```

Note: If you get an error such as: `error while loading shared libraries: libpocketsphinx.so.3`, you may want to check your linker configuration of the LD_LIBRARY_PATH environment variable described below:

```BASH
export LD_LIBRARY_PATH=/usr/local/lib
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
```

### Installation

```BASH
sudo apt-get install bison
sudo apt-get install swig
cd sphinxbase-5prealpha
./autogen.sh
.configure
make
sudo make install
export LD_LIBRARY_PATH=/usr/local/lib
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
cd ../pocketsphinx-5prealpha
./autogen.sh
.configure
make
sudo make install
```


### Example of Running with C

Contents of decode.c

```BASH
gcc -o decode decode.c
```

```CPP
#include <stdlib.h>
#include <stdio.h>
#include <time.h>
#define BILLION  1000000000.0;


int main(void) {
    struct timespec start, end;

    system("export LD_LIBRARY_PATH=/usr/local/lib");
    system("arecord --format=S16_LE --duration=5 --rate=16k -D sysdefault:CARD=1 --file-type=wav testfiles/noisy.wav");
    system("echo done recording...");
    system("python testfiles/noiseClean.py");
    system("echo done cleaning...");
    clock_gettime(CLOCK_REALTIME, &start);
    system("\
        pocketsphinx_continuous \
        -infile testfiles/filtered.wav \
        -dict dicts/8050.dic \
        -lm dicts/8050.lm \
        2>./output/unwanted-stuff.log | tee ./output/words.txt");
    // pocketsphinx_continuous -infile testfiles/Untitled.wav -dict dicts/8050.dic -lm dicts/8050.lm 2>./output/unwanted-stuff.log | tee ./output/words.txt
    system("echo done decoding...");
    clock_gettime(CLOCK_REALTIME, &end);
    double time_spent = (end.tv_sec - start.tv_sec) +
            (end.tv_nsec - start.tv_nsec) / BILLION;
    char *timerOutput = malloc(25);
    sprintf(timerOutput, "echo Time Elapsed: %f\n", time_spent);
    system(timerOutput);
}
```


### System Mic Noise Fix

Using system/USB mic has noises, to clean, here is the content of noiseClean.py:

```python
outname = 'testfiles/filtered.wav'

cutOffFrequency = 400.0

# from http://stackoverflow.com/questions/13728392/moving-average-or-running-mean
def running_mean(x, windowSize):
  cumsum = np.cumsum(np.insert(x, 0, 0))
  return (cumsum[windowSize:] - cumsum[:-windowSize]) / windowSize

# from http://stackoverflow.com/questions/2226853/interpreting-wav-data/2227174#2227174
def interpret_wav(raw_bytes, n_frames, n_channels, sample_width, interleaved = True):

    if sample_width == 1:
        dtype = np.uint8 # unsigned char
    elif sample_width == 2:
        dtype = np.int16 # signed 2-byte short
    else:
        raise ValueError("Only supports 8 and 16 bit audio formats.")

    channels = np.fromstring(raw_bytes, dtype=dtype)

    if interleaved:
        # channels are interleaved, i.e. sample N of channel M follows sample N of channel M-1 in raw data
        channels.shape = (n_frames, n_channels)
        channels = channels.T
    else:
        # channels are not interleaved. All samples from channel M occur before all samples from channel M-1
        channels.shape = (n_channels, n_frames)

    return channels

with contextlib.closing(wave.open(fname,'rb')) as spf:
    sampleRate = spf.getframerate()
    ampWidth = spf.getsampwidth()
    nChannels = spf.getnchannels()
    nFrames = spf.getnframes()

    # Extract Raw Audio from multi-channel Wav File
    signal = spf.readframes(nFrames*nChannels)
    spf.close()
    channels = interpret_wav(signal, nFrames, nChannels, ampWidth, True)

    # get window size
    # from http://dsp.stackexchange.com/questions/9966/what-is-the-cut-off-frequency-of-a-moving-average-filter
    freqRatio = (cutOffFrequency/sampleRate)
    N = int(math.sqrt(0.196196 + freqRatio**2)/freqRatio)

    # Use moviung average (only on first channel)
    filtered = running_mean(channels[0], N).astype(channels.dtype)

    wav_file = wave.open(outname, "w")
    wav_file.setparams((1, ampWidth, sampleRate, nFrames, spf.getcomptype(), spf.getcompname()))
    wav_file.writeframes(filtered.tobytes('C'))
    wav_file.close()
```
