# Autonomous Navigation and Vision-Based Control for a Mobile Robot in ROS2

![FRA2MO Robot Navigating in Gazebo](placeholder.gif) 
## 🤖 Project Overview

This repository contains a complete autonomous navigation software framework for a differential drive mobile robot (`rl_fra2mo`). The project was developed using ROS2 and the Nav2 stack, and it is simulated in the Gazebo environment. The primary goal is to perform waypoint following, environment mapping, and vision-based tasks, demonstrating a comprehensive understanding of modern robotic navigation systems.

---

## ✨ Key Features

* **Custom Gazebo Simulation**: The project launches in a modified Gazebo world (`leonardo_race_field`) with the robot spawned at a specific initial pose. The world includes repositioned obstacles and strategically placed ArUco markers for vision tasks.
* **Waypoint Following with Nav2**: Implements multi-goal navigation using the Nav2 Simple Commander API. The robot follows a predefined sequence of four goals loaded from a YAML file. The executed trajectory is recorded and analyzed using `ros2 bag`.
* **Environment Mapping & SLAM Tuning**: Utilizes the Nav2 stack to generate a complete map of the environment. The project involved tuning key SLAM and costmap parameters across multiple configurations to analyze the impact on map accuracy, trajectory execution, and timing.
* **Vision-Based Navigation Task**: Integrates the `aruco_ros` package to perform a complex, vision-guided task. The robot autonomously navigates to an object, uses its camera to locate an ArUco marker, retrieves the marker's pose relative to the map, and returns to its initial position.

---

## 🛠️ Core Technologies & Skills

* **Frameworks & Libraries**: ROS2, Nav2, Gazebo, RViz, `aruco_ros`
* **Languages**: Python, YAML
* **Robotics Concepts**:
    * **Navigation**: Simultaneous Localization and Mapping (SLAM), Motion Planning, Obstacle Avoidance, Costmaps (Global and Local)
    * **Control**: Waypoint Following, Behavior Trees (via Nav2)
    * **Computer Vision**: Fiducial Marker Detection (ArUco)
    * **Simulation & Tooling**: SDF (Gazebo world files), TF2 (Transform System), `ros2 bag`

---

## 📋 Implementation Highlights

This project was implemented as a series of distinct but interconnected tasks:

1.  **Waypoint Navigation**: A Python script uses the Nav2 Simple Commander API to send a sequence of goals to the robot, waiting for each one to be completed before sending the next.

2.  **SLAM and Parameter Tuning**: The robot explores the environment to build a map. Key parameters within `slam.yaml` and `explore.yaml` were systematically modified to observe their effect on performance.

3.  **Vision-in-the-loop Task**: A high-level state machine was implemented where the robot first navigates to the proximity of a specific obstacle. It then activates a search behavior to find the ArUco marker. Upon detection, it computes the marker's world-frame pose and publishes it before navigating home.

---

## :white_check_mark: Usage
### 1. Autonomous navigation task
Run the simulation environment through the following launch file (by default the Gazebo simulation starts in the "PAUSED" state. To make it play click on the bottom left "Play" button):
```
ros2 launch rl_fra2mo_description gazebo_fra2mo.launch.py
```
Run the RViz visualization tool and the publisher nodes through the following launch file:
```
ros2 launch rl_fra2mo_description display_fra2mo.launch.py
```
Then bring up the Nav2 package and start the SLAM Toolbox through the unified "fra2mo_run" launch file:
```
ros2 launch rl_fra2mo_description fra2mo_run.launch.py
```
#### :bangbang: Attention
Before proceeding, make sure that the fra2mo robot has spawned in the correct pose, as we observed that the robot's position is not always correctly initialized. If not, try stopping the display_fra2mo and/or the fra2mo_run launch files and re-running them.
At the time of writing, the correct spawning of the robot seems a random feature of the code execution, although it may be related to the "Back in time jumps" performed during the simulation.

Once ensured that the environment is correctly set up, launch the follow_waypoints.py scripts to see fra2mo move around the map:
```
ros2 run rl_fra2mo_description follow_waypoints.py
```

### 2. Enviroment mapping tuning the navigation stack's parameters
Run the simulation environment through the following launch file (by default the Gazebo simulation starts in the "PAUSED" state. To make it play click on the bottom left "Play" button):
```
ros2 launch rl_fra2mo_description gazebo_fra2mo.launch.py
```
Run the RViz visualization tool and the publisher nodes through the following launch file:
```
ros2 launch rl_fra2mo_description display_fra2mo.launch.py
```
Then bring up the Nav2 package and start the SLAM Toolbox through the unified "fra2mo_run" launch file, specifying not to load a map (the default behaviour of the launch file) by passing as an input parameter the path to an example slam configuration file:
```
ros2 launch rl_fra2mo_description fra2mo_run.launch.py slam_params_file:=/home/user/ros2_ws/src/rl_fra2mo_description/config/slam_no_map.yaml
```
#### :bangbang: Attention: As pointed out in the 'Autonomous navigation task' section, make sure that the robot spawns in the correct pose.

Then run the map_everything.py script which makes fra2mo to explore the map based on the specified config file:    
```
ros2 run rl_fra2mo_description map_everything.py
```

### 3. Vision-based navigation of the mobile platform
Run the simulation environment through the following launch file (by default the Gazebo simulation starts in the "PAUSED" state. To make it play click on the bottom left "Play" button):
```
ros2 launch rl_fra2mo_description gazebo_fra2mo.launch.py
```
Run the RViz visualization tool and the publisher nodes through the following launch file:
```
ros2 launch rl_fra2mo_description display_fra2mo.launch.py
```
Then bring up the Nav2 package and start the SLAM Toolbox through the unified "fra2mo_run" launch file, specifying the aruco_retrieve flag to true. In this way, the ROS Vision framework is brought up and a static broadcaster which sends the transform from the 'map' frame to the 'aruco' frame is executed:
```
ros2 launch rl_fra2mo_description fra2mo_run.launch.py aruco_retrieve:=true
```
#### :bangbang: Attention: As pointed out in the 'Autonomous navigation task' section, make sure that the robot spawns in the correct pose.

Then run the ArucoRetrieve script which causes fra2mo to approach Obstacle 9, trigger the ArUco marker recognition, broadcast its pose and then making the robot return to its initial position:
```
ros2 run rl_fra2mo_description ArucoRetrieve.py
```
