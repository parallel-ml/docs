# Running Live SLAM

To run live SLAM, you need the `nimashoghi/ubuntu-xenial-orb-slam2-apps` iamge. If you're running this on the Raspberry Pi (which uses the arm32v7 architecture), you will need the `nimashoghi/arm32v7-ubuntu-xenial-orb-slam2-apps` image.

The command given to the `orb-slam2-apps` image follows the following structure:

`<operation> <vocabulary file> [...args]`

- Operation should be set to `slam_app` for offline SLAM and `slam_app_live` for live SLAM.
- The `vocabulary file` parameter should be set to `/root/ORBvoc.txt`.
- The rest of the arguments depend on the operation.

### `slam_app` Operation
`Usage: slam_app <vocabulary> [<name> <type=mono|stereo|both> <settings_mono> <settings_stereo> <left_image_folder> <right_image_folder> <times_file>]...`

### `slam_app_live` Operation
`Usage: slam_app_live <vocabulary> [<name> <settings> <left_image_url> <right_image_url>]`

**Important note: All file inputs to this app can be URLs instead. If they are URLs, they are read over the network instead.**

## Running Live SLAM with MJPEG Video Input
To run live SLAM with MJPEG video input, you need to do the following:
- Set the `operation` parameter to `slam_app_live`.
- Feed the video stream URL into the image input parameter.

Example `docker-compose.yml` is shown below:
```yaml
version: "3.1"
services:
    slam_live:
        image: nimashoghi/ubuntu-xenial-orb-slam2-app:latest
        command: "slam_app_live /root/ORBvoc.txt http://192.168.0.114:8080/settings-live-stereo.yml http://192.168.0.116:8080/?action=stream&param=.mjpg http://192.168.0.116:8081/?action=stream&param=.mjpg"
        environment:
            DISPLAY: 192.168.0.114:0.0
        volumes:
            - "./output/:/output/"
```

## Running Live SLAM with Linux Video Device (/dev/videoN) Input
Running live SLAM with Linux video device input is very similar to the MJPEG case. The only differences are:
- The Docker container must be ran as priviledged (or the device must be exposed explicitly, see Docker documentation for more detail).
- The input video streams must be integers that indicate the number of the video input. For example, if your v

Example `docker-compose.yml` is shown below. This example runs the livestream using the `/dev/video0` and `/dev/video1` devices as the left and right video inputs, respectively:
```yaml
version: "3.1"
services:
    slam_live:
        image: nimashoghi/ubuntu-xenial-orb-slam2-app:latest
        command: "slam_app_live /root/ORBvoc.txt http://192.168.0.114:8080/settings-live-stereo.yml 0 1"
        environment:
            DISPLAY: 192.168.0.114:0.0
        volumes:
            - "./output/:/output/"
```

## Running Offline SLAM
To run offline SLAM, you use the `slam_app` operation. In this case, instead of giving MJPEG streams (or Linux video device IDs), we give a folder containing a set of JPEG files. Additionally, we provide a `times.txt` file which maps each image to a specific timestamp.

Example `docker-compose.yml` for offline SLAM:
```yaml
version: "3.1"
services:
    slam:
        image: nimashoghi/arm32v7-ubuntu-xenial-orb-slam2-app:latest
        command: "slam_app /root/ORBvoc.txt  MH01 mono http://192.168.0.113:8080/euroc_mono.yaml http://192.168.0.113:8080/euroc_stereo.yaml http://192.168.0.113:8080/euroc_MH01/cam0/data http://192.168.0.113:8080/euroc_MH01/cam1/data http://192.168.0.113:8080/euroc_MH01/timestamps.txt"
        privileged: true
        volumes:
            - "./output/:/output/"
```

### Monocular SLAM vs. Stereo SLAM
The `type` parameter decides whether we should run mono SLAM or stereo SLAM. Note that you will need to have the proper calibration settings, as well as 2 separate video inputs, if run stereo SLAM. If you're running mono SLAM, you can leave the 2nd video input as empty.


## Output
After running offline or online SLAM, the program will output its predicted trajectory to the following file `/output/{name}_keyframe_{stereo|mono}.csv`. Many popular datasets, like the EuRoC dataset, provide ground-truth data. You can compare your predicted values to the ground-truth by calculating the absolute trajectory error (ATE) or the relative pose error (RPE). [View the following link for more information.](https://vision.in.tum.de/data/datasets/rgbd-dataset/tools)
