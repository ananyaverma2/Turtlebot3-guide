# Guide to working on Turtlebot3

# Installation 

Before starting with the installation process, make sure that you have ROS installed on your system.

## Install the ROS dependencies

*$ sudo apt-get install ros-melodic-joy ros-melodic-teleop-twist-joy ros-melodic-teleop-twist-keyboard ros-melodic-laser-proc ros-melodic-rgbd-launch ros-melodic-depthimage-to-laserscan ros-melodic-rosserial-arduino ros-melodic-rosserial-python ros-melodic-rosserial-server ros-melodic-rosserial-client ros-melodic-rosserial-msgs ros-melodic-amcl ros-melodic-map-server ros-melodic-move-base ros-melodic-urdf ros-melodic-xacro ros-melodic-compressed-image-transport ros-melodic-rqt* ros-melodic-gmapping ros-melodic-navigation ros-melodic-interactive-markers*

## Install Turtlebot3 Packages

* *$ mkdir -p ~/catkin_ws/src*
* *$ cd ~/catkin_ws/src/*
* *$ git clone -b melodic-devel https://github.com/ROBOTIS-GIT/DynamixelSDK.git*
* *$ git clone -b melodic-devel https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git*
* *$ git clone -b melodic-devel https://github.com/ROBOTIS-GIT/turtlebot3.git*
* *$ cd ..* 
* *$ catkin_make*
* *$ echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc*

## Set the Turtlebot3 model

*$ echo "export TURTLEBOT3_MODEL=<name_of_the_model>" >> ~/.bashrc*

There are three models available to choose from :  **burger, waffle, waffle_pi**

## Installing the simulations

* *$ cd ~/catkin_ws/src/*
* *$ git clone -b melodic-devel https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git*
* *$ cd ..*
* *$ catkin_make*

## Check to see if the installation is complete

In order to check if the installation is properly done, try runnning a world and move the turtlebot3 using your keyboard.

### Terminal 1

*$ roslaunch turtlebot3_gazebo turtlebot3_empty_world.launch*

### Terminal 2

Launch the teleop node to control the turtlebot3 using the keyboard.

*$ roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch*

Use the keys **w, a, d, x, s** to move the robot.

**NOTE :** If you are not able to move the robot using the keyboard, check if the gazebo packages are installed, if not use:

*$ sudo apt-get install ros-melodic-gazebo-ros-pkgs*

**Now we are ready to work on the Turtlebot3**

# Mapping

For mapping the environment in which the robot is present, **SLAM (Simultaneous Localization and Mapping)** is used.

## Create a map of the environment

### Terminal 1

*$ roscore*

### Terminal 2

Launch the environment in which you want the robot to work. Here we will use the default world.

*$ roslaunch turtlebot3_gazebo turtlebot3_world.launch*

### Terminal 3

Launch the teleop node to operate the robot such that it can move in the environment and generate a map of the surroundings.

*$ roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch*

### Terminal 4

Now, launch the slam node to create a map.

*$ roslaunch turtlebot3_slam turtlebot3_slam.launch*

Move the turtlebot so that it can scan the entire place. Once you are finished, the terminal will look like this:

![gmapping](/pictures/gmapping.png)

## Save the map

In order to save the map for navigation, we will run the map_server node. The map uses two-dimensional Occupancy Grid Map (OGM).

*$ rosrun map_server map_saver -f ~/<path_of_the_directory>/<name_of_map>*

Once the map is saved, you will see two files:
* <name_of_map>.pgm
* <name_of_map>.yaml

![map pgm](/pictures/map.png)
![map yaml](/pictures/map_yaml.png)


# Localization

Localization is the process of determining where a mobile robot is located with respect to its environment. For this, we will use the navigation node to launch the map yaml file which we obtained previously.

*$ roslaunch turtlebot3_navigation turtlebot3_navigation.launch map_file:=$HOME/<path_of_the_directory>/<name_of_map>.yaml*

**NOTE :** Make sure, you have roscore, the environment and the teleop terminals running.

The RViz window will look like this:

![Localization](/pictures/arrows.png)

You can see in the image that the laser readings (green/red) do not match the map. Also, you will find alot of green arrows spread all over, hence we will manually localize the turtlebot, such that the arrows are more condensed. 

Click on the 2d pose estimate button in rviz and click on the map where the actual robot is located and drag the large green arrow toward the direction where the robot is facing..
Repeat this process until the laser sensor data is overlayed on the saved map. you can use turtle teleop to move the robot and localize it properly such that the arrows are more condensed. Refer to the gazebo terminal to find the accurate position an orientation of the turtlebot.

Once, the localization is done (green arrows are condensed), the window will look like:

![Localization](/pictures/l_done.png)

**NOTE :** Do not forget to stop the teleop node as it can hinder with the navigation.

# Navigation

Finally, we can navigate the robot from one place to another.

Click on the 2D nav goal button in RViz and drag the cursor to the place where you want the robot to navigate, the back of the arrow determines x, y values and the angle at which the arrow is tilted gives the theta.

Set the position where you want the turtlebot to go (red arrow)
![Path](/pictures/1.png)

The robot starts moving to that location.

![Path](/pictures/2.png)
![Path](/pictures/3.png)
![Path](/pictures/4.png)

You can see two lines when the turtlebot is moving:

* **Yellow** - The local planner path, this is calculated using the youbot sensor readings.
* **Red** - The global planner path, this path is calculated before the robot starts moving as it is using the static map, so it does not take into account the youbot sensor readings. Hence, if there are some changes in the environment, it will not effect the path.

**NOTE :** This tutorial is created using the [Turtlebot3 Manual](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/#overview). The main aim is to provide a simple, step-by-step and condensed tutorial.

