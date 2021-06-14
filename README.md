# CarND-Capstone-Project-ROS
Self-Driving Car Engineer Nanodegree Program
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

![](./imgs/capstone_intersection.gif)

## Abstract
The goal of this project is to enable a self driving car, driving around a given test track 
using waypoint navigation and stopping on traffic lights. 
Every of this waypoints gets its x,y - coordinates of the track and a specific target velocity, 
depending on the traffic light signal and the distance to it.<br> 
The Control - System uses the throttle, steering and brakes to successfully navigate the
waypoints within the correct target velocity.<br> 
So in this project components of
- Perception 
- Planning 
- Controlling  

are implemented in a ROS-framework that works with [Autoware](https://github.com/Autoware-AI/autoware.ai).
This framework is provided by Udacity along with a simulator to test the code.

Watch the car in [action](https://youtu.be/mR1VSxhwBT4).

Overview
---
1. System Architecture
2. Perception 
3. Planning
4. Control
5. Appendix: *Build Instructions & Simulator* ...

## 1) System Architecture
The following system architecture diagram shows the ROS nodes and topic used in this project. 
The ROS topics are described briefly in the **Code Structure** section below.

<figure>
 <img src="./imgs/system_architecture.png" width="600" alt="System architecture" />
 <figcaption>
 <p></p> 
 <p style="text-align: center;"> Fig. 1: System architecture. </p> 
 </figcaption>
</figure>
 <p></p>

## 2) Perception
This package contains the traffic light detection node: `tl_detector.py`. 
This node takes in data from the `/image_color`, `/current_pose`, and `/base_waypoints` topics and 
publishes the locations to stop for red traffic lights to the `/traffic_waypoint` topic.

The `/current_pose` topic provides the vehicle's current position and `/base_waypoints` provides 
a complete list of waypoints the car will be following.
In this project the  traffic light detection node is implemented and a traffic light classification node
could be added in the future, since it is not in the focus of this project.
Traffic light detection takes place within `tl_detector.py`. 

<figure>
 <img src="./imgs/tl_node.png" width="360" alt="Traffic Light Node" />
 <figcaption>
 <p></p> 
 <p style="text-align: center;"> Fig. 2: Traffic Light Detection Node. </p> 
 </figcaption>
</figure>
 <p></p>
 
To enhance the project further a traffic light classifier can be trained and integrated, similar to my 
project [traffic sign classifier](https://github.com/DiegelD/Traffic_Sign_Classifier).
Also an object detection could be integrated like in this [repo](https://github.com/udacity/CarND-Object-Detection-Lab). 
This should than take place within this file `../tl_detector/light_classification_model/tl_classfier.py`.
The necessary infrastructure is already provided there. However this wasn`t in the scope of this project.

## 3) Planning
This package contains the waypoint updater node: `waypoint_updater.py`.
The purpose of this node is to update the target velocity property of each waypoint based on
traffic light detection data. This node will subscribe to the /base_waypoints, 
`/current_pose`, `/obstacle_waypoint`, and `/traffic_waypoint topics`, and publish a list of waypoints
ahead of the car with target velocities to the `/final_waypoints` topic.

<figure>
 <img src="./imgs/planning_node.png" width="360" alt="Waypoint Updater" />
 <figcaption>
 <p></p> 
 <p style="text-align: center;"> Fig. 3: Planning Node, Waypoint Updater. </p> 
 </figcaption>
</figure>
 <p></p>

## 4) Control
The self driving car is equipped with a drive-by-wire (dbw) system, meaning the throttle, brake, 
and steering have electronic control. This package contains the files that are responsible for
control of the vehicle: the node `dbw_node.py` and the file `twist_controller.py`, along with a `pid.py` 
and lowpass filter `lowpass.py` that are implemented. The dbw_node subscribes to
the `/current_velocity` topic along with the `/twist_cmd` topic to receive target linear and angular
velocities. Additionally, this node will subscribe to `/vehicle/dbw_enabled`, which indicates if the 
car is under dbw or driver control. This node will publish throttle, brake, and steering commands
to the `/vehicle/throttle_cmd`, `/vehicle/brake_cmd`, and `/vehicle/steering_cmd` topics.

<figure>
 <img src="./imgs/control_node.png" width="360" alt="Control Node" />
 <figcaption>
 <p></p> 
 <p style="text-align: center;"> Fig. 4: Drive by Wire Node. </p> 
 </figcaption>
</figure>
 <p></p>

## Appendix

In addition to these packages you will find the following, which are provided by Udacity.
The styx and styx_msgs packages are used to provide a link between the simulator and ROS, and to
provide custom ROS message types:

### path_to_project_repo)/ros/src/styx/

A package that contains a server for communicating with the simulator, and a bridge to translate and publish simulator messages to ROS topics.

### path_to_project_repo)/ros/src/styx_msgs/

A package which includes definitions of the custom ROS message types used in the project.

### path_to_project_repo)/ros/src/waypoint_loader/

A package which loads the static waypoint data and publishes to /base_waypoints.

### path_to_project_repo)/ros/src/waypoint_follower/

A package containing code from Autoware which subscribes to /final_waypoints and publishes target vehicle linear and angular velocities in the form of twist commands to the /twist_cmd topic.

Please use **one** of the two installation options, either native **or** docker installation.

### Native Installation

* Be sure that your workstation is running Ubuntu 16.04 Xenial Xerus or Ubuntu 14.04 Trusty Tahir. [Ubuntu downloads can be found here](https://www.ubuntu.com/download/desktop).
* If using a Virtual Machine to install Ubuntu, use the following configuration as minimum:
  * 2 CPU
  * 2 GB system memory
  * 25 GB of free hard drive space

  The Udacity provided virtual machine has ROS and Dataspeed DBW already installed, so you can skip the next two steps if you are using this.

* Follow these instructions to install ROS
  * [ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu) if you have Ubuntu 16.04.
  * [ROS Indigo](http://wiki.ros.org/indigo/Installation/Ubuntu) if you have Ubuntu 14.04.
* Download the [Udacity Simulator](https://github.com/udacity/CarND-Capstone/releases).

### Docker Installation
[Install Docker](https://docs.docker.com/engine/installation/)

Build the docker container
```bash
docker build . -t capstone
```

Run the docker file
```bash
docker run -p 4567:4567 -v $PWD:/capstone -v /tmp/log:/root/.ros/ --rm -it capstone
```

### Port Forwarding
To set up port forwarding, please refer to the "uWebSocketIO Starter Guide" found in the classroom (see Extended Kalman Filter Project lesson).

### Usage

1. Clone the project repository
```bash
git clone https://github.com/udacity/CarND-Capstone.git
```

2. Install python dependencies
```bash
cd CarND-Capstone
pip install -r requirements.txt
```
3. Make and run styx
```bash
cd ros
catkin_make
source devel/setup.sh
find /home/workspace/your/directory -type f -iname "*.py" -exec chmod +x {} \;
roslaunch launch/styx.launch
```
4. Run the simulator

### Real world testing
1. Download [training bag](https://s3-us-west-1.amazonaws.com/udacity-selfdrivingcar/traffic_light_bag_file.zip) that was recorded on the Udacity self-driving car.
2. Unzip the file
```bash
unzip traffic_light_bag_file.zip
```
3. Play the bag file
```bash
rosbag play -l traffic_light_bag_file/traffic_light_training.bag
```
4. Launch your project in site mode
```bash
cd CarND-Capstone/ros
roslaunch launch/site.launch
```
5. Confirm that traffic light detection works on real life images

### Other library/driver information
Outside of `requirements.txt`, here is information on other driver/library versions used in the simulator and Carla:

Specific to these libraries, the simulator grader and Carla use the following:

|        | Simulator | Carla  |
| :-----------: |:-------------:| :-----:|
| Nvidia driver | 384.130 | 384.130 |
| CUDA | 8.0.61 | 8.0.61 |
| cuDNN | 6.0.21 | 6.0.21 |
| TensorRT | N/A | N/A |
| OpenCV | 3.2.0-dev | 2.4.8 |
| OpenMP | N/A | N/A |

We are working on a fix to line up the OpenCV versions between the two.
