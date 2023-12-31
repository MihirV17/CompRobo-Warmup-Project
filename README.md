# CompRobo-Warmup-Project
## By Kenta Terasaki, Mihir Vemuri
For the warmup comprobo project we explored the robot platform for the course, a Neato vacuuming robot with a raspberry pi controller board. We configured it with ROS(Robot Operating System), Python in VS Code, simulated our code in the Gazebo simulator, and debugged utilizing rviz and rqt. Our main task in this project was to equip the neato with a set of behaviors, which we did utilizng our control scheme. The programs we created were a teleoperation mode for direct user control, wall following, obstacle avoidance, person following, square driving, and a finite state controller to switch between behaviors.

#### Neato Robot Platform
![image](https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/c2cb5824-0c3d-429f-99ec-4282c8016da7)

# Teleop Mode
## Planning State Diagram
![image](https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/3a7c7d4d-9480-404a-8efd-c3b91199a8e4)
## Description
This script provides a basic yet effective means of teleoperating a robot via keyboard input in a ROS 2 environment, allowing for simple control of its movements. It leverages the rclpy library for ROS 2 communication and handles key presses to control a robot's linear and angular velocities. The script defines a class named TeleopKeyboard, which inherits from rclpy's Node class. This class initializes a ROS 2 node with the name 'teleop_keyboard' and creates a publisher to send velocity commands of the Twist message type to the 'cmd_vel' topic. It also sets up a timer that periodically invokes a callback function called timer_callback. Within this callback, the script monitors keyboard input, determining the key pressed, and accordingly adjusts the linear and angular velocities in the Twist message to control the robot's movement (e.g., 'w' for forward, 's' for backward, 'a' for left, 'd' for right). If no valid key is pressed, the robot stops. The main function serves as the entry point of the script, initializing the ROS 2 system, handling keyboard input in raw mode, and continuously processing ROS 2 messages. Any exceptions are caught and displayed, and the terminal settings are restored upon script termination. 

# Drive Square
## Planning State Diagram
![image](https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/9ea1f47c-25db-4bea-9261-6bdcf6b37e71)

## Description
This script orchestrates a robot to move in a square pattern. In the __init__ constructor, it initializes a ROS 2 node, sets up a publisher for Twist messages on 'cmd_vel', and schedules a timer with a run_loop callback. Various variables like turns_executed, executing_turn, and timing parameters manage the robot's state. The start_segment method records segment start times (straight or turn), and is_segment_done checks for completion based on a specified duration. execute_turn and execute_straight create Twist messages for angular (turn) and linear (straight) velocities. The run_loop is the central control logic. It checks segment completion, alternates between turns and straight movements, updates robot state, and prints debug info. The main function initializes ROS 2, creates a DriveSquare instance, enters the ROS 2 spin loop for control, and shuts down ROS 2 when finished. The script is flexible, allowing parameter adjustments for side length, time per side, and turn duration, making it adaptable to various square patterns or movement needs.


# Person Follow
## Planning State Diagram
![image](https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/77d767d7-e872-4543-b340-e741c1546543)

## Description
This script enables a robot to follow a person by adjusting its orientation and forward movement based on laser scanner data, making it useful for applications such as person tracking or following in robotics. In the PersonFollower class, the constructor initializes a ROS 2 node named 'person_follower'. It creates a publisher for sending Twist messages to control the robot's velocity ('cmd_vel') and subscribes to laser scanner data from the 'scan' topic. Additionally, it sets up a timer with a callback named follow_person, which controls the robot's behavior over time. The laser_callback function is invoked when laser scan data is received. It analyzes the data to find the minimum distance and corresponding angle where a person might be detected. This angle is stored in the self.scan variable. The core functionality is implemented in the follow_person method. It calculates the velocity required to follow a person based on the angle of detection (self.scan). If the person is detected within certain angle thresholds (approximately in front of the robot), it maintains the robot's orientation without movement. If the person is detected to the right, the robot rotates counterclockwise, and if to the left, it rotates clockwise. Additionally, if the person is detected within certain forward-facing angles, the robot moves forward at a fixed linear velocity. The main function initializes the ROS 2 system, creates an instance of the PersonFollower class, enters the ROS 2 spin loop to handle communication and control logic, and finally shuts down the ROS 2 system upon completion.

## Person Follow Video
https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/c5092172-cc47-4feb-8de9-bd56f890b878

# Obstacle Avoidance
## Planning State Diagram
![image](https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/154e4760-fa6b-4288-aeed-d19e11714049)

## Description
This script enables a robot to autonomously navigate and avoid obstacles by analyzing laser scan data, making it suitable for obstacle avoidance tasks in robotics applications.
Upon initialization, the class sets up essential components, including a publisher for sending Twist messages to control the robot's velocity ('cmd_vel') and a subscriber to gather data from the laser scanner ('scan'). A timer is established to trigger the avoid_obstacles function every 0.1 seconds ('ovr_time'). The laser_callback method is invoked whenever laser scan data is received. It stores the scan data, which provides distance measurements around the robot, enabling it to detect obstacles. The core obstacle avoidance logic is implemented in the avoid_obstacles function. It calculates the minimum distance within a specified forward-facing range (from 0 to 10 degrees and 350 to 360 degrees) using the laser scan data. If the minimum distance is less than a predefined threshold (1.0 in this case), it triggers a turning action to avoid the obstacle. The robot either turns left or right based on whether the closest obstacle is on the left or right side and stops moving forward. If there are no immediate obstacles, the robot proceeds with a slow forward motion and a slight angular correction to navigate. The main function initializes the ROS 2 system, creates an instance of the ObstacleAvoidance class, enters the ROS 2 spin loop to manage communication and control logic, and finally, shuts down the ROS 2 system upon completion.

## Obstacle Avoidance Video
https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/93e789f9-73fd-4259-93f7-d2eb77242dcf


# Wall Follow and Finite State Controller
## Wall Follow Planning State Diagram
![image](https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/cd5dad3e-18e4-4b69-a06e-58baba451a08)
## Description
This program enables autonomous wall-following behavior for a robot using ROS. The WallFollow class serves as the core controller. It maintains forward_distance and backward_distance variables, representing front and rear laser scanner measurements. In the constructor (__init__), it sets up a ROS Node, schedules a loop to run at 10 Hz, and creates publishers for robot velocity commands (self.neato_pub) and laser scanner data subscriptions (self.scan_sub). The scan_callback function updates distance variables based on laser data, aiding debugging. In run_loop, it computes the required angular speed to maintain a set distance from a wall. The calculated angular speed is then used to create a Twist message, which contains angular and linear velocity components. The angular velocity is set based on the calculated value, while the linear velocity is set to move the robot forward at a constant rate. Finally, the Twist message is published to the cmd_vel topic using self.neato_pub, which sends the control commands to the robot's motors.This message is published via self.neato_pub to control the robot's motors. The main function initializes the ROS Node, creates a WallFollow instance, and enters a ROS loop for continuous execution. This program autonomously guides the robot along a wall, responding to laser scanner data.


## Finite State Controller Planning State Diagram
![image](https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/cf413f97-cc45-4b91-8554-3aa916edadaa)
## Description
A finite state controller is a program or device that operates in distinct states, switching between them based on predefined rules or inputs.It operates through a finite state controller defined in the WallFollow class. Upon initialization, the class sets up laser scan and bump sensor subscriptions and creates a control timer. In the run_loop function, the robot alternates between "wall_follower" and "hit_obstacle" states. In the "wall_follower" state, it adjusts its angular speed based on laser scan data to follow walls while maintaining a safe linear velocity. Upon encountering an obstacle, it transitions to "hit_obstacle," executing a predefined sequence of reversing and rotating. The script provides detailed console debugging information. The main function initializes ROS 2, runs the controller, and closes ROS 2 upon completion. This script provides a versatile framework for autonomous navigation, especially in constrained environments, and adapts to obstacles efficiently.

## Finite State Controller/Wall Follow Video

https://github.com/MihirV17/CompRobo-Warmup-Project/assets/123433158/98d319d8-470a-42e4-a562-bf6161382cbf






