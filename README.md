
<h1>Homework 3</h1>
<p>Anna Garverick</p>

<h2>Part I: Differential Drive</h2>


The diff_drive package spawns a differential drive bot that will drive forwards and flip over itself in a gazebo world.

To run the simulation, launch the launchfile ddrive.launch from the diff_drive package.

    roslaunch diff_drive ddrive.launch

The specifications of the robot may be adjusted in the robot_params.yaml file. The dimensions and mass of the body and wheels are controlled through parameters in this file. 

![DDrive Gif](ddrive.gif)

Longer Video: https://youtu.be/OywEfLBxLX4


<h2>Part II: Motion Planning </h2>

The arm_move package faciliates planning a trajectory for a pincherx100 arm using MoveIt, and either simulating the motion in Gazebo or controlling the robot in the real world. It uses three services to set the world, recieve and follow incremental waypoints, then play back the planned trajectory. 

<h4>Restart:</h4>
    
    rosservice call /px100/restart "clear_waypoints: false
        box_x: 0.15
        box_y: 0.0 
        box_z: 0.025"

The restart service spawns the realsense box at the given box_x, box_y, and box_z position. If clear_waypoints is true, an empty list will be pushed to the parameter server. If clear_waypoints is false, the node will start with a list of waypoints from the parameter server. This is useful if you want to reset the scene while the node is running. If clear_waypoints is set to false during the initial run, default points will be pulled from the waypoints.yaml file. 

<h4>Step:</h4>
        
        rosservice call /px100/step "{x: 0.30593, 
            y: 0.062275, 
            z: 0.024178, 
            rx: -0.010597, 
            ry: 0.10511, 
            rz: 0.099754, 
            rw: .98939, 
            gripper: 1}" 

The step service takes a pose input with three point coordinates and a quarterion orientation, and an argument to open or close the gripper. The service will attempt to plan and follow a trajectory to the pose, and return a MoveIt Error Code. If the trajectory was successful, it appends the pose to the list of waypoints and updates the server parameter. If the gripper argument is set to 1, the gripper will close after the pose is reached. If the gripper argument is set to 0, the gripper will open after the pose is reached. Any other argument will be ignored, so the gripper will stay in the same state. The gripper command is also added to the waypoint list. 

<h4>Follow:</h4>

        rosservice call /px100/follow "cycle: true"

The follow service plays back the succesful planning steps saved in the waypoints parameter. If the cycle argument is set to true, the arm will cycle through the waypoints repeatedly until the cycle parameter is set to false from another command line window. 


<h3>MoveIt / RViz</h3>

https://youtu.be/31kQbKZzFnE

        roslaunch arm_move arm.launch "mode":=rviz

With the rviz mode, the PX100 is launched in RViz with a table collision object and a real sense box are loaded with the restart call. Paths may be planned directly with MoveIt, or with the step and follow services. 

<h3>Gazebo</h3>

![Gazebo Gif](gazebo.gif)

https://youtu.be/OdsFyToxo4Q

        roslaunch arm_move arm.launch "mode":=gazebo

With the gazebo mode, a custom world with is launched in Gazebo with a real sense box and a small object. The launch file loads the PX100 arm model into the Gazebo world, and which will follow the paths sent through MoveIt (also through services or in the GUI). 

<h3>Real</h3>

![Real Gif](real.gif)

https://youtu.be/NZ8W95a07rM

        roslaunch arm_move arm.launch "mode":=real

Real mode sends commands from MoveIt to the PX100 arm in the real world. 