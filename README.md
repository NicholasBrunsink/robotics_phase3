# Implementation of a vision guided robotic system on the UR5 system - Phase 3

### Github Repos used
- ur5e_control: https://github.com/hsaeidi-uncw/ur5e_control 
    * specifically the ```gripper``` branch
- robot_vision_lectures: https://github.com/hsaeidi-uncw/robot_vision_lectures
- robot_filtering_lectures: https://github.com/hsaeidi-uncw/robot_filtering_lectures 
    * for reference of filtering implementation ***(not used for running code)***

### Helpful ROS docs
- http://wiki.ros.org/

---
---
## **Ros flow**
### **Prerequisites:**
1. Run roscore
    * ```roscore```
2. Connecting to robot
    + In Simulator
      >```roslaunch ur_gazebo ur5e_bringup.launch```
    + Real Robot
      >```rosrun ur5e_control ur5e_ros_connection.sh```
3. Start ur5e_controller
    * >```roslaunch ur5e_control ur5e_controller.launch```
        * In Simulator:  ```<param name="sim" value="true"/>```
        * Real Robot:  ```<param name="sim" value="false"/>```
4. Get Camera Data
   + In Simulator
      >```cd ~/Downloads && ./play_data.sh```
   + Real Robot
      >```roslaunch realsense2_camera rs_rgbd.launch```
5. Collect and process 2D and 3D data form camera
    * >```rosrun robot_vision_lectures crop_visualize_3D```
    * >```rosrun robotics_report2 detect_ball.py```
    * >```rosrun robotics_report2 sphere_fit.py```
6. Start Trajectory Calculator
    * >```rosrun ur5e_control task_space_traj```
7. Start Frame Publisher
    * >```roslaunch ur5e_control frame_publisher.launch```
8. Start rviz to see frames, sphere apprximation, and robot movement
    * >```rosrun rviz rviz```
9. Start rqt_gui to send boolean values to /toggleInit and /toggleMove
    * >```rosrun rqt_gui rqt_gui```
10. In rqt_gui: 
    - Add message publisher for /toggleInit, initialized to true
    - Add message publisher fo /toggleMove, initialized to false
---
## **Running movement plan:**

### **First**
1. Start camera to robot frame converter
    * >```rosrun robotics_report2 cam_to_bot.py```
2. Start pickup_motion script to start generating plans
    * >```rosrun robotics_report2 pickup_motion.py```
3. Leave both running

### **Then**
1. Publish True to /toggleInit topic to begin sphere tracking and initial condition collection.
2. Run initialization script
    * In Simulator
      >```rosrun robotics_report2 brun_init.py```
    * Real Robot (if not auto initialized)
      >```rosrun ur5e_control manual_initialization_real_robot.py```
3. Wait for satisfactory initial conditions
4. Stop initialization script
5. Publish False to /toggleInit to stop initialization and pause tracking
6. Publish True to /toggleMove to begin the plan execution

### **To Generate and execute a new plan**
1. Stop ```ur5e_control task_space_traj``` node
2. Publish False to /toggleMove to stop plan publishing
3. Publish True to /toggleInit to resume tracking
4. Run initialization script (if not already in good position)
    * In Simulator
      >```rosrun robotics_report2 brun_init.py```
    * Real Robot
      >```rosrun ur5e_control manual_initialization_real_robot.py```
5. Stop initialization script when done
6. Publish False to /toggleInit to pause tracking
7. Start trajectory calculator
    * >```rosrun ur5e_control task_space_traj```
8. Publish True to /toggleMove to start new plan when ready
