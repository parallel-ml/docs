# Webcam Video/Image
Install streamer:
```bash
sudo apt-get install streamer
```

Record video or capture 
```bash
streamer -q -c /dev/video0 -r 10 -t 00:00:20 -s 640x480 -o ~/test0000.jpeg
streamer -q -c /dev/video0 -f rgb24 -t 00:01:30 -r 10 -s 640x480 -o ~/outfile.avi
```

Convert to .mov to compress, then transfer. The options is set to work with Quick Time, but VLC works with any option (e.g., audio format and pixel format)
```bash
ffmpeg -i outfile.avi -acodec libmp3lame -ab 192 -pix_fmt yuv420p -r 9 output.mov
```
