Goal: Build a 3D point cloud map of a space using an RGBD camera and work with this point cloud in grasshopper

Hardware: TurtleBot2 with a NUC(with ubuntu 20.04); Astra orbbec camera (RGBD); laptop (with windows 11 and ubuntu 20.04)

Software: ROS Noetic; Docker; rhino/gH

There is also a rplidar package. Because a lidar can be used for mapping as well. I didnt succeed in making the package work yet

===========================================

Establish a ssh connection between the laptop and nuc

Install some general tools on both:
git nano vim wget curl terminator htop

Install Docker on the NUC and Laptop. Follow the instructions in the documentation. \
To download without a GUI on the NUC, follow this link. \
NOTE: the latest deb package is a download file. So download into your laptop and copy into the NUC: \
scp <path_to_folder_on_laptop> <path_to_folder_on_nuc> 

Install ROS-noetic (desktop full install) and all its dependencies from here.

Install extra ros tools on the laptop: \
sudo apt install python3-catkin-tools python3-rosdep2 ros-noetic-rviz ros-noetic-rqt \
ros-noetic-rosbash ros-noetic-rqt-action \
ros-noetic-rqt-console ros-noetic-rqt-graph ros-noetic-rqt-topic \
ros-noetic-rqt-tf-tree

git pull this repository into your workspace (mapping_tbot). The repository for astra camera is being pulled while building the container

The main package being used for this mapping exercise is octomap_server \
The launch file within this is octomap_mapping.launch. It needs to be customized a little to work with. \
first: 
<!-- fixed map frame (set to 'map' if SLAM or localization running!) -->
		<param name="frame_id" type="string" value="odom_combined" />
The "odom_combined" value should be changed to the map frame we are working with. Check this in rviz. It is the value of ‘fixed frame’. I change this to “odom” or “map”

second: 
<!-- data source to integrate (PointCloud2) -->
		<remap from="cloud_in" to="/narrow_stereo/points_filtered2" /> 
This needs to be the topic that has the point cloud data. In this case, with the astra camera, it is the topic “/camera/depth/points”

Understand how octomap builds the point cloud: \
Sparse point clouds in OctoMap might be due to the way OctoMap handles occupancy information. OctoMap utilizes an octree data structure to represent the environment. This data structure divides the space into cubes, and each cube is either marked as occupied, free, or unknown. The default parameters and the resolution of the octree may result in a sparse representation, especially in areas where the robot hasn't explored much. \
In the octomap_mapping.launch file there is a resolution parameter. Change that and see what happens \
In the octomap_mapping_nodelet.launch file there is sensor_model/max_range value. Play with that. \
octomap_server.cfg file has parameters of how the algorithm builds point clouds into the octree. Play with this 

##notes \
Increasing the resolution of the map made it super slow \
The floor plane is being excluded. Change the parameter in the cfg file that excludes the floor from true to false if you like. 

Depending on the speed of your wifi, you may or may not need to compress the image data on the nuc and then decompress them to view on the laptop. If you need to, follow these documentations: \
http://wiki.ros.org/image_transport
http://wiki.ros.org/compressed_image_transport
http://wiki.ros.org/compressed_depth_image_transport
Remember to subscribe to these new topics in the octomap launch file

Ssh into the nuc and build the docker image in the workspace. Then run it.

From inside the container,open up a terminator/terminal and run the following commands to set the ros master on the nuc: \
source devel/setup.bash \
export ROS_MASTER_URI=http://<ip_address_of_the_nuc>:11311 \
export ROS_IP=<ip_address_of_the_nuc>:11311 \
  Check the ip of the system with: hostname -I \
  This needs to be done on every new docker terminal that is opened 

You need to run multiple files at the same time, so you need multiple terminals running the same docker container. To do so, make sure all the terminals are running the nuc workspace through ssh and then run the following docker commands: \
docker ps \
  Gives you the container id of running dockers \
docker exec -it <container> bash \
  Opens the running container in that terminal 

Then run the following commands to start mapping the space: \
roslaunch turtlebot_bringup minimal.launch \
  This is to start the turtlebot \
roslaunch turtlebot_teleop keyboard_teleop.launch \
  This is navigate the turtlebot using the keyboard \
roslaunch astra_camera astra.launch \
  To launch the camera \
roslaunch octomap_server octomap_mapping.launch 

From your laptop workspace, run the following: \
export ROS_MASTER_URI=http://<ip_address_of_the_nuc>:11311 \
export ROS_IP=<ip_address_of_the_LAPTOP>:11311 \
rviz \
  MarkerArray - Marker topic  >>  occupied_cells_viz_array \
You will now have a visual of the point cloud present within the camera's field of view. Move around using the teleop keys

Back in the container workspace of the nuc: \
rosrun octomap_server octomap_saver -f my_octomap.bt \
  To save the point cloud you just built \
rosrun octomap_server octomap_server_node my_octomap.bt \
  to view the saved point cloud \
rosrun octomap octree2pointcloud <input.bt> <output.pcd> \
  converting .bt file to .pcd \
pcl_pcd2ply <input.pcd> <output.ply> \
  converting .pcd to .ply 

scp the ply file outside docker environment to view with grasshopper

Work with the pointcloud in grasshopper using the volvox plugin: \
https://www.youtube.com/watch?v=oLI8Kr9ddNk \
Tutorial has - how to get e57 files and visualize point cloud, make a section of point cloud, subsampling, converting points to mesh (just one plugin)
