# Live Stream Raspberry Pi Video Across the Network

We have built, containerized, and uploaded the `mjpeg-streamer` program into the `nimashoghi/streamer` image. You can use this image to stream a mjpeg stream over HTTP.

## Using a USB Camera

Run the `streamer` image with the `input_uvc` input plugin and the `output_http` output plugin.

`docker run -t -i --privileged -p 8080:8080 nimashoghi/streamer -i 'input_uvc.so --device /dev/video0' -o output_http.so`

Make sure to change `/dev/video0` to reflect the Linux device file path.

[Settings for the `input_uvc` plugin are shown here.](https://github.com/jacksonliam/mjpg-streamer/blob/master/mjpg-streamer-experimental/plugins/input_uvc/README.md)

### Stereo Cameras
For stereo cameras, you can run two separate instances of the `nimashoghi/streamer` image.

Example `docker-compose` configuration is shown below:
```yaml
version: "3.1"
services:
    video0:
        image: nimashoghi/streamer
        privileged: true
        ports:
            - "8080:8080"
        command: -i 'input_uvc.so --device /dev/video0' -o output_http.so
    video1:
        image: nimashoghi/streamer
        privileged: true
        ports:
            - "8081:8080"
        command: -i 'input_uvc.so --device /dev/video1' -o output_http.so
```

## Using the Pi Camera Module

Run the `streamer` image with the `input_raspicam` input plugin and the `output_http` output plugin.

`docker run -t -i --privileged -p 8080:8080 nimashoghi/streamer -i input_raspicam.so -o output_http.so`

[Settings for the `input_raspicam` plugin are shown here.](https://github.com/jacksonliam/mjpg-streamer/blob/master/mjpg-streamer-experimental/plugins/input_raspicam/README.md)

## View the Stream
To view the stream, visit the URL `http://{RASPBERRY_PI_LOCAL_IP}:8080/?action=stream` in your browser. You can also open this network stream with VLC to record the stream as a `.avi` file.
