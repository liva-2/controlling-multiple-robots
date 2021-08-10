# controlling-multiple-robots
## 1- Create a project in ROS Development Studio(ROSDS)
* first we have to create an account in ROSDS then we create a new roject then we select ROS kinetic after that we have to run the roject then we open the command line and the IDE from (ROSDS)
## 2- Create a package
* We’ll create a package using these commands:
```
cd ~/catkin_ws/src
```
```
catkin_create_pkg multi_robot rospy gazebo_ros
```
## 3- create folders
* we’ll create a folder called launch under the package directory in the IDE
* after creating the launch folder we'll create 3 launch files
### the first one called (one_robot.launch) and we'll write these line under the file
```
<launch>
    <arg name="robot_name"/>
    <arg name="init_pose"/>

    <node name="spawn_minibot_model" pkg="gazebo_ros" type="spawn_model"
     args="$(arg init_pose) -urdf -param /robot_description -model $(arg robot_name)"
     respawn="false" output="screen" />

    <node pkg="robot_state_publisher" type="state_publisher" 
          name="robot_state_publisher" output="screen"/>

    <!-- The odometry estimator, throttling, fake laser etc. go here -->
    <!-- All the stuff as from usual robot launch file -->
</launch>
```
### the second one called (robots.launch) and we'll write these line under the file
```
<launch>
  <!-- No namespace here as we will share this description. 
       Access with slash at the beginning -->
  <param name="robot_description"
    command="$(find xacro)/xacro.py $(find turtlebot_description)/robots/kobuki_hexagons_asus_xtion_pro.urdf.xacro" />

  <!-- BEGIN ROBOT 1-->
  <group ns="robot1">
    <param name="tf_prefix" value="robot1_tf" />
    <include file="$(find multi_robot)/launch/one_robot.launch" >
      <arg name="init_pose" value="-x 1 -y 1 -z 0" />
      <arg name="robot_name"  value="Robot1" />
    </include>
  </group>

  <!-- BEGIN ROBOT 2-->
  <group ns="robot2">
    <param name="tf_prefix" value="robot2_tf" />
    <include file="$(find multi_robot)/launch/one_robot.launch" >
      <arg name="init_pose" value="-x -1 -y 1 -z 0" />
      <arg name="robot_name"  value="Robot2" />
    </include>
  </group>
</launch>
```
### the last one called (main.launch) and we'll write these line under the file
```
<launch>
  <param name="/use_sim_time" value="true" />

  <!-- start world -->
  <node name="gazebo" pkg="gazebo_ros" type="gazebo" 
   args="$(find turtlebot_gazebo)/worlds/empty_wall.world" respawn="false" output="screen" />

  <!-- start gui -->
  <!-- <node name="gazebo_gui" pkg="gazebo" type="gui" respawn="false" output="screen"/> -->

  <!-- include our robots -->
  <include file="$(find multi_robot)/launch/robots.launch"/>
</launch>
```
## 4- launch the simulation
* You can launch the simulation with the following command
```
roslaunch multi_robot main.launch
```
* Then you have to open the gazebo window from Tools->Gazebo
* you should see 2 robots are spawned in the simulation
## 5- Move the robot
* Open another terminal and type:
```
rosrun teleop_twist_keyboard teleop_twist_keyboard.py /cmd_vel:=/robot1/cmd_vel
```
### now you can control the robots using teleop
