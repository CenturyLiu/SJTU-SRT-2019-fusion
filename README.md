# SJTU-SRT-2019-fusion
SJTU-SRT-2019-fusion is the ROS package containing the perception, fusion and route management part of the SRT-2019 project. Before proceeding, please refer to [SJTU-SRT-2019](https://github.com/CenturyLiu/SJTU-SRT-2019) for backgrounds.
## Ideology
The ROS nodes belong to the fusion package perform the following tasks in series: 
* Detect the position of the cones from the lidar raw data (x,y,z position in the car's coordinate system; the direction the car is heading to is positive x; axes follow right hand rule) and the camera rectified image (the position is represented by a rectangle region, paramatrized by start and end x-y)

* Fuse the perceived cone positions from lidar and camera raw data. Fusion is done by projecting the 3D points to the 2D image plane, and determine whether the projected points are within the rectangle regions that contain cones detected by image detection.

* Determine the left and right lanes by the color of the cones(red-left; blue-right) and find the target point for navigation. First count the number of red cones and blue cones determined. If red's number is bigger than or equal to the blue's(if numbers are equal, the number should be at least 2), follow the left lane. If the blue's number is bigger, follow the right lane. If both color has only one determined cone, choose the mid point of those two cones as the target. If only one cone or no cone is detected, stop car.

## Code illustration
All the codes for this package are written in python and stored under the "scripts" folder. They can be divided into 3 differnt nodes completing different tasks:
* node1: detect cones from lidar raw data
　　related file: <br>
　　cone_visualization_v7.py <br>
　　Effects: <br>

* node2: detect cones from rectified color image
　　related files: <br>
　　image_process.py　<br>
　　Effects: <br>
　　detection.py　<br>
　　Effects:<br>
  
  　model related files: 
　　variables(folder), checkpoint, config.json, index, model.ckpt-504050.data-00000-of-00001, model.ckpt-50450.index, model.ckpt-50450.meta, model.ckpt-5814.meta, pipeline.config, saved_model.pb <br>
    


* node3: cone fusion and control
