# SJTU-SRT-2019-fusion
SJTU-SRT-2019-fusion is the ROS package containing the perception, fusion and route management part of the SRT-2019 project. Before proceeding, please refer to [SJTU-SRT-2019](https://github.com/CenturyLiu/SJTU-SRT-2019) for backgrounds.
## Ideology
The ROS nodes belong to the fusion package perform the following tasks in series: 
* Detect the position of the cones from the lidar raw data (x,y,z position in the car's coordinate system; the direction the car is heading to is positive x; axes follow right hand rule) and the camera rectified image (the position is represented by a rectangle region, paramatrized by start and end x-y)

* Fuse the perceived cone positions from lidar and camera raw data. Fusion is done by projecting the 3D points to the 2D image plane, and determine whether the projected points are within the rectangle regions that contain cones detected by image detection.

* Determine the left and right lanes by the color of the cones(red-left; blue-right) and find the target point for navigation. First count the number of red cones and blue cones determined. If red's number is bigger than or equal to the blue's(if numbers are equal, the number should be at least 2), follow the left lane. If the blue's number is bigger, follow the right lane. If both color has only one determined cone, choose the mid point of those two cones as the target. If only one cone or no cone is detected, stop car.

## Code illustration
All the codes for this package are written in python and stored under the "scripts" folder. They can be divided into 3 differnt nodes completing different tasks:
* node1: detect cones from lidar raw data <br>
　　related file: <br>
　　cone_visualization_v7.py <br>
　　    Effects:   This is the ROS node for cone detection from raw lidar data.The node is consistently receiving data from 　　　　　　the lidar.<br>     　　　　　　Once the raw data is received, the node will unpack the raw data and get 3D data points in terms of the 　　　　　　lidar coordinate system.  <br> 　　　　　　Then we clustring the raw data and judge whether a cluster belongs to the cone based on its height and 　　　　　　width. <br>　　　　　　After finishing the detection, the node will pose all the detected cones in terms of the　<br> 　　　　　　cone_pos_whole.msg data type.

* node2: detect cones from rectified color image　<br>
　　related files: <br>
　　image_process.py　<br>
　　    Effects: The ROS node for detecting the cones from image color. Once the node receive a rectified image, <br> 　　　　　　it first utilize the function "detection.py" to detected the position of the cones. Then we crop the <br>　　　　　　 detected cones and use function cone_color_detection.py to check the color of the cones. <br>　　　　　　Finally, the node will pose the detected cones with their position in the image and their color, in terms of <br> 　　　　　　the "imagel_whole.msg" data type. <br>
　　detection.py　<br>
　　    Effects: function for using pretrained model to detect cones. The folder containing this function should also be　<br> 　　　　　　with the files listed below.<br>
  
  　model related files: 
　　variables(folder), checkpoint, config.json, index, model.ckpt-504050.data-00000-of-00001, model.ckpt-50450.index, model.ckpt-50450.meta, model.ckpt-5814.meta, pipeline.config, saved_model.pb <br>
  
 　　cone_color_detection.py <br>
 　　    Effects:　Function for detecting the color of the cones. The input must be an image of a cropped cone in terms of <br> 　　　　　　RGB. Given the input, the function will turn the image to HSV space and then detect the color based on HSV <br> 　　　　　　thresholds. <br>
　　　
* node3: cone fusion and control　 <br>
　　related files: <br>
　　lidar_cam_fusion.py<br>
　　    Effects: ROS node for sensor fusion and route management. The node receives detection results from both <br> the lidar and image process node(node1 and node2 described above) and relate results to each other by comparing the time stamp. Once a pair of lidar and image data is found, we use the function lidar2img.py to inject the 3D cone position from the lidar onto the image plane. Then the node compare the injected points with the cone position in the image from node 2.<br> Then we're done with the fusion process, and have got cone points with both 3D position and color. Finally, we use the function get_target.py to get the target point for navigation and use the pure persuit model to get the control parameters (speed and rotation angle of the front tires). <br>
　　lidar2img.py<br>
　　    Effects: function that inject input 3D points onto the image plane based on the principle of camera imaging.<br>
　　get_target.py<br>
　　    Effects: function that get the target points based on the lane information.<br>
