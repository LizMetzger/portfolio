--- 
layout: post
title: Extended Kalman Filter SLAM from Scratch
image: /videos/SLAM_demo.gif
author: Liz Metzger
---

(C++, ROS2)

{% include youtube.html id="wWTfin3Q1rQ" %}

In this project I implemented an extended kalman filter SLAM algorithm from scratch on a simulated turtlebot3. The simulation could be viewed in Rviz and the whole project was done leveraging ROS2 and C++. A turtlebot3 is a differential drive robot with 360 degree lidar data and it was known that the robot would be exploring an environment with cylindrical objects which it would need to detect and identify. 

The first element of this project was generating an odometry estimation of where the robot was. Odometry of the robot was calculated using a 2D transformation and matrix math library which was used to calculate the inverse kinematics of the robot given velocity inputs for each wheel. Calculating odometry provided an estimation of where the robot was assuming the kinematics worked as perfectly in theory as they did in calculation. This provided a baseline assumption of where the robot would be assuming the wheels did not slip and the robot did not collide with an obstacle. In practice these odometry calculations are unreliable and can not be used to solely estimate the position of the robot, although it may be sufficient for a highly controlled environment or short distances.

Next I put together the perception pipeline which handled simulating and processing the lidar data. The lidar from the robot was simulated following the specifications of the actual robot and was used to identify the presence of new obstacles. It did this by first clustering the points in the lidar data together based on distance into groups of points that were likely representing the same object. These clusters were then processed by a circle fitting algorithm to identify clusters that represented cylindrical objects of a specific size. Since the size of the obstacles were known beforehand it was trivial to filter the calculated circles based on radius to leave only objects that likely represented the objects we were looking for. This information could be used to generate a belief on where the robot was relative to the obstacles. 

Once objects were found I was able to perform object identification on them to distinguish them from obstacles that had been seen before or new obstacles based on the Mahalanobis distance. If the distance was above my set threshold then the found object would be stored as a new obstacle or it would be matched to a previously existing obstacle. 

The Kalman Filter combined the data about where the robot was according to odometry with the processed lidar data to create a more accurate belief about where the robot was relative to the obstacles. Each SLAM update took the belief about where the robot should have moved relative to the odom frame and adjusted the location of the odom frame relative to the map fram to make the odometry calculation align closer with the lidar data. How much it would adjust the frame was based on the weighting of my P, Q, and R matrices in the Kalman Filter which were based on my confidence in the readings provided by the sensors vs the odometry calculations. After many trials I found weights that seemed to accurately represent the behavior of the robot.


![SLAM TF Tree](https://github.com/LizMetzger/Sloth-bot/assets/113066141/8adda018-15a0-4fd5-8985-4648a24f7448)


In the simulation the real position of the obstacles and robot were represented with red cylinders and the algorithm’s best estimation was represented in green. The blue robot model represents the position of the robot based on the odometry calculations.


Check out my [GitHub Repo](https://github.com/LizMetzger/EKF_SLAM)!