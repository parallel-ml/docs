# CMU Sphinx
I used the pocketsphinx to decode the audio files on the raspberry pi’s. I installed it on the raspberry pi by following these instructions: https://cmusphinx.github.io/wiki/tutorialpocketsphinx/#installation-on-unix-system
Then I used the pocketsphinx_continuous command line command. There are multiple options, such as -inmic, which while use the system’s default microphone to detect and live decode the speech. You can also decode files using the -infile flag, then type the directory of the file relative to where you are calling the command from. You can also change the dictionary and the language model that the program uses by using the -dict and -lm flags. I created my own dictionary and language model using a tool I found online (link below), specifically made for pocketsphinx. I did this so that we could reduce the language model size to improve performance and accuracy. I found that the performance was 6x faster when I used my reduced dictionary, and obviously the accuracy is better, but it loses flexibility. The next steps are to increase the dictionary to include a more variety of words, and increase the flexibility of commands that can be given to the raspberry pi. Below I have attached pictures of terminal output that shows the difference in performance. The output on the top shows performance with smaller dictionary and language model, the output on the bottom is the original dictionary that pocketsphinx comes with. It took more than 6x longer and it was less accurate.

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


Originally it verbosely outputs every step while it processes the audio, and it was hard to find the actual output, so I created a command to output all the unwanted logs to a specific file, and the actual decoded speech into it’s own file.

(Dict and LM tool): http://www.speech.cs.cmu.edu/tools/lmtool-new.html
