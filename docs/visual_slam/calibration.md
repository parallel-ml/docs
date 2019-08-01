# Camera Calibration

The cameras that we use will have some distortion. Calibration is the process of figuring out and undoing this distortion. The process is more involved with stereo cameras.

For our purposes, we have created a `calibration` docker image

## Calibration
To calibrate your camera, you can use the `nimashoghi/calibration` Docker image. Use the `./calibration` command for mono calibration and `./calibration_stereo` for stereo calibration.

### Monocular Calibraion
Below is the `docker-compose.yml` file for mono calibration:
```yaml
version: "3.1"
services:
    calibration_mono:
        image: nimashoghi/calibration
        environment:
            - DISPLAY=192.168.0.113:0.0
        volumes:
            - "./calib/:/output"
            - "./assets/config.xml:/app/assets/config.xml"
```

### Stereo Calibraion
Below is the `docker-compose.yml` file for stereo calibration:
```yaml
version: "3.1"
services:
    monocular:
        image: nimashoghi/calibration
        environment:
            - DISPLAY=192.168.0.114:0.0
        volumes:
            - "./calib_stereo/:/output"
            - "./assets/:/app/assets/"
            - "../snapshots/output/:/app/assets/stereo_input/"
        command: ./calibration_stereo /app/assets/stereo_calib.xml
```


### Other resources
-   [Camera calibration With OpenCV](https://docs.opencv.org/2.4/doc/tutorials/calib3d/camera_calibration/camera_calibration.html)
-   [Camera Calibration and 3D Reconstruction](https://docs.opencv.org/2.4/modules/calib3d/doc/camera_calibration_and_3d_reconstruction.html)
