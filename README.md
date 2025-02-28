# stretch3_documentation

This repository is created to compare ROS1 and ROS2 functionalities in Stretch-3 mobile manipulator from hello robot.

##  Software development with ROS2 and Python

We can develop software using Python and ROS2 in stretch robot. Moroever, Stretch Body Robot API is provided by manufacturer to write Python codes to move the robot.

## [Navigation](https://docs.hello-robot.com/0.3/getting_started/demos_mapping_and_navigation/)

Stretch can interact with environment 
1) using on-board 2D lidar with **Nav2** (ROS2 Navigation Stack) as `stretch_nav2` and 
2) using Intel depth camera with Stretch's own navigation stack **FUNMAP** (Fast Unified Navigation,Mapping and Planning) as `stretch_funmap` , which is also available in earlier version of Stretch robot compatible with ROS1.

# 1) [stretch_nav2](https://github.com/hello-robot/stretch_ros2/tree/humble/stretch_nav2)

[Nav2](https://docs.nav2.org/index.html) is official navigation stack of ROS used in various kind of robotics. It is used for perception, planning, control, localization, visualization. It uses behaviour trees for decision making in navigation.
It uses task server to compute cost function.

Here are steps to create 2D map using `stretch_nav2` package.

1) Map the space robot will navigate (can be done with joystick) and save 2D generated map.
2) If robot's environment is dynamic, we can use `nav2_amcl` package. Robot will localize itself while navigating in previously defined 2D map. We can also save the updated map.

# 2) [stretch_funmap](https://github.com/hello-robot/stretch_ros2/tree/humble/stretch_funmap#overview)

FUNMAP stands for fast unified navigation, manipulation and planning.  It replicates human environment with Max Height Images(MHIs). 

In contrast to other environments such as point clouds and 3D mesh models, MHIs support fast,efficient operations through optimized image processing.MHIs also have the potential to represent enclosed surfaces (e.g., surfaces in shelves and cabinets) and vertical surfaces (e.g., doors) by defining new VOIs with different orientations and heights, which is a capability that merits future exploration.

FUNMAP uses approximate geometric models and computer-vision algorithms to efficiently find plans that take advantage of its telescopic arm and cartesian structure.

The primary difference between `stretch_nav2` and `stretch_funmap` is generation of map. 

As `stretch_nav2` creates a 2D map at the height of lidar, approximately 6.5" inches above the ground. There is a limitation that it will only see legs of table, chair, lamp or object which is on the ground. In this case, sometimes it is difficult, because things such as tables have wide structure on the top. Robot's base can go there easily but it's head can be collided with table's top. 

Hence, It is necessary to generate 3D map to avoid this problem. `stretch_funmap` helps us to overcome this problem. It takes an advantage of eye-heighted Intel D435if depth camera to build the map.

Here are steps to create 3D map using `stretch_funmap` package.

1) Leave the robot in clear space so it can move freely and execute mapping launch file
2) Execute teleop launch file to move the robot. After pressing spacebar in same terminal, we can initialize the head scan. It will pan the camera around the room, rotate its base and pan camera again. Do that in each new location to create and merge the map.

We can also visualise the 3D map in Rviz2 and avoid collision due to uneven objects.

FUNMAP enables the Stretch to reach a 3D target position. FUNMAP models the linear motion of the telescoping arm as it extends to reach a target as a line in a manipulation cost image. FUNMAP uses a navigation cost image and Djikstra’s algorithm with a priority queue to efficiently estimate the cost of navigating to all floor locations. FUNMAP then combines these results to find a minimum cost plan that increases the distance to obstacles and the manipulable workspace of the robot.
