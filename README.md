# Testing the Realsense T265 Accuracy
Some brief results of tracking accuracy tests that I ran with the Realsense T265 V-SLAM camera

In June 2019 I ran some tests of the T265 mounted to a UR5 robotic arm, trying to assess the accuracy of this camera. As of writing I could not find any actual figures on how well the tracking of this camera performs, Intel mentions a *generous* figure of 1 percent in one of their support responses [here](https://www.intel.com/content/www/us/en/support/articles/000032899/emerging-technologies/intel-realsense-technology.html). 

## Experiment Setup
To get a ground truth I have a UR5 cobot arm mounted to a table in a lab setting. There is plenty of texture and good lighting for the T265 to operate with. 

The arm is controlled through ROS using the ur_modern_driver low bandwidth trajectory follower. See my other repo for details on this https://github.com/jaspereb/UR5_With_ROS_Moveit_Tutorial. The T265 is run using the default ROS launch files provided by https://github.com/IntelRealSense/realsense-ros so all of the image settings are left untouched from these. 

The T265 is mounted to the end of the arm using a 3D printed plate, approximately 60mm deep from the mounting point. This introduces a small amount of flex (<1mm in position). Prior to starting, the end effector (camera) is placed in a pose with zero roll and pitch so that the T265 odometry frame can be aligned with the world frame using only data from the CAD files of the robot, camera and mounting bracket. 

I construct the /tf tree in ROS so that there is a `world->t265_link` transform which is the camera position built using the camera odometry, as well as a `world->t265_link_from_arm` transform which is the camera position built using the arm encoders. You can check the full /tf tree [here](frames.pdf).

After setting up the /tf tree the arm is run through 2 trajectories. The first is a cartesian one, moving along the x axis then the z axis (in an L shape). The second is a random trajectory with complicated orientation changes. For a final experiment I run the complicated trajectory again, but with the cameras on the T265 completely covered, to test how good the IMU is. 

Each of these 3 experiments is recorded into a ROS bag. This is played into matlab where the `world->t265_link` and `world->t265_link_from_arm` are converted into X-Y-Z-Roll-Pitch-Yaw matrices called `camPose` and `armPose` respectively. 

## Results Files
Included in the repo are matlab files for each of the experiment runs. These are arrays of the 6 DoF pose over time as estimated from the arm encoders, and from the T265. You can also use scipy.io to open these.

## Caveats
