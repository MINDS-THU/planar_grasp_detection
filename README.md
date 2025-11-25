## Overview
This package implements a modular grasp detection pipeline built around three ROS services. The system detects antipodal grasp candidates, evaluates them using a convolutional neural network, and converts the selected grasp into a 3D gripper pose. The components are implemented as ROS services to allow easy experimentation and flexible swapping of modules. Although service calls introduce some communication overhead, this design has been convenient for rapid development and debugging.

## ROS Services
The package consists of three primary services:

1. **GraspSampling.srv**  
2. **GraspDetection.srv**  
3. **GraspConversion.srv**

### GraspSampling
This service adapts code from [GQ-CNN](https://github.com/BerkeleyAutomation/gqcnn) to generate candidate antipodal grasps from a depth image. For each sampled grasp, the system extracts a square RGB patch centered at the grasp location. The patch size is set to **1.2× the gripper width**, resulting in patches of different scales depending on object distance.

### GraspDetection
This service evaluates each image patch using a convolutional neural network based on the architecture from  
*Supervision via Competition: Robot Adversaries for Learning Tasks*.  
Before inference, all patches are resized to **224×224×3**.

The network predicts the probability of a successful grasp for **18 discrete rotation angles** (0°, 10°, …, 170°). These probabilities are then combined with the antipodal angle estimate using a weighting matrix to produce a final score for each grasp candidate.

### GraspConversion
The final service takes the grasp detection results along with the depth image and converts the selected grasp rectangle into a robot gripper pose. The conversion follows the procedure described in *Deep Learning for Detecting Robotic Grasps*.

The implementation is currently parameterized for the **Robotiq 85 gripper**. Users with different end-effectors should adjust the `FINGER_LENGTH` parameter accordingly.

## Example Output
Below is a screenshot from the application.  
The right window shows detected grasp rectangles, and the arrows in the main window represent the converted gripper poses.

![demo_img](https://github.com/cyrilli/planar_grasp_detection/blob/master/img/demo_image.png?raw=true)

## Demo Video
The following video demonstrates the ROS package performing tabletop grasping in a Gazebo simulation.

[![Robot Grasp Pose Detection Demo](http://img.youtube.com/vi/qlhEtHCVZsw/0.jpg)](https://www.youtube.com/watch?v=qlhEtHCVZsw)
