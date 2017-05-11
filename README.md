# ROS package to calibrate a camera and a LiDAR.

The package was used to calibrate a Velodyne VLP-16 LiDAR with a ZED Stereo camera. Point Gray Blackfly was also calibrated against VLP-16.

The package uses `aruco_ros` and a slightly modified `aruco_mapping` as dependencies, both of which are available on this repository itself.


### Contents
1. [Setup](#setup)
2. [Getting Started](#getting-started)
3. [Usage](#usage)
4. [Future Improvements](#future-improvements)

### Setup
Prerequisites:

[ROS](http://www.ros.org/)
[aruco_ros]
[aruco_mapping]

ROS package for the camera and LiDAR you wish to calibrate.
Clone this repository to your ROS workspace.

### Getting Started

There are a couple of configuration files that need to be specfied in order to calibrate the camera and the LiDAR. The config files are available in the `cross_sensor_calibration/conf` directory.

`config_file.txt`

>1280 720
>-2.5 2.5
>-4.0 4.0
>0.0 2.5
>0.05
>2
>0
>611.651245 0.0        642.388357 0.0
>0.0        688.443726 365.971718 0.0
>0.0        0.0        1.0        0.0

The file contains specifications about the following:

>image_width image_height
>cloud_filter_x- cloud_filter_x+
>cloud_filter_y- cloud_filter_y+
>cloud_filter_z- cloud_filter_z+
>cloud_intensity_threshold
>number_of_markers
>use_camera_info_topic?
>camera_matrix

cloud_filter_i- and cloud_filter_i+ are in meters.

`marker_coordinates.txt`

>2
>48.4
>46.8
>4.0
>5.0
>20.5
>49.0
>46.8
>4.0
>5.0
>20.5

The first line specfies 'N' the number of boards being used. Followed by N*5 lines with the following information about the dimensions of the board:
>width
>height
>border horizontal width of ArUco marker
>border vertical width of ArUco marker
>edge length of ArUco marker

All dimensions are in centimeters.

`cross_sensor_calibration.yaml`

>cross_sensor_calibration:
>  camera_frame_topic: /frontNear/left/image_raw
>  camera_info_topic: /frontNear/left/camera_info
>  velodyne_topic: /velodyne_points

Contains name of camera and velodyne topics that the node will subscribe to.


### Usage

Before launching the calibration node ensure that the ArUco markers are visible in the camera frame and the markers are arragned in ascending order of their `ArUco ids` from left to right as viewed by the camera. Use the following command to start the calibration process.

```shell
proslaunch cross_sensor_calibration find_velodyne_points.launch
```

An initial [R|t] between the camera and the various ArUco markers will be estimated. Following this, a filtered point cloud (according to the specifications in the `config_file.txt`) will be displayed. The user needs to mark each edge of the rectangular board.

Each board will have 4 line segments and need to be marked from leftmost board to the rightmost board. Marking a line segment is quite straight-forward, one needs to draw a quadrilateral around the line being marked. Click and press a key to confirm the corner of the quadrilateral. Once 4 points are clicked, each followed by a key-press, the program will move on to the next line segment. Continue marking the line segments for all boards until complete.
Line segments for each board are to be marked in clock-wise order starting from the top-left.

After marking all the line-segments, the rigid-body transformation between the camera and the LiDAR frame will be displayed.


### Future improvements

- [ ] automate process of marking line-segments
- [ ] iterative process with weighted average over multiple runs
