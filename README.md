# Custom-3d-human-pose-dataset
Custom your own 3d human pose dataset with motion capture system and cameras

## Human3.6m format datset in [videopose3d](https://github.com/facebookresearch/VideoPose3D) or other similar 3d pose model

### 1. Collecting 3d pose data by motion capture system, 2d pose data by monocular camera
In our dataset, we using vicon system to capture 3d pose information, which need 17 markers in joint at least.

![h36m keypoint](https://github.com/floriculture/Custom-your-own-3d-human-pose-dataset/blob/main/pic/Human3.6M%20KPT2.png)

The three-dimensional human pose in the Human36m dataset contains 32 annotated keypoints. Some keypoints are annotated to satisfy the rotation vector representation, but in reality, we only need 17 keypoints. The data format used in videopose3d also contains only 17 keypoints, as shown in the diagram above. You also need camera to capture video for extracting 2d pose.

### 2. Frame rate synchronization

The sampling frequency in motion capture system is generally higher than that of regular camera. In our custom dataset, the frame rate of the VICON motion capture device is 200, which is much higher than the camera frame rate of 60. So we need to downsample the data collected by the motion capture device。

### 3. Camera Calibration
As is well known, the same 3D pose will generate different 2D poses under cameras placed in different positions. To solve the problem of pose ambiguity, we need to represent the 3D pose in the camera's coordinate system, which requires camera calibration to help us solve this problem.

The 3D keypoint data collected by motion capture devices are in the world coordinate system, which generally has significant differences from the camera coordinate system. We can represent the 3D pose in the camera coordinate system through rigid body transformation. One coordinate system is referred to as A, and the other coordinate system is referred to as B. These two coordinate systems can be converted to each other through rigid body transformation. The EPnP method is a recommended approach to calibrate the rotation matrix and translation vector of these two coordinate systems. The rotation matrix and translation vector from the world coordinate system to the camera coordinate system are generally referred to as the camera extrinsic parameters. However, using the EPnP method to calibrate the camera extrinsic parameters also requires obtaining the camera intrinsic parameters.

![EPnP Method](https://github.com/floriculture/Custom-your-own-3d-human-pose-dataset/blob/main/pic/pic8%20PnP%E5%87%A0%E4%BD%95%E7%BB%93%E6%9E%84.PNG)

Camera intrinsic parameters refer to the internal parameters of the camera, including focal length, unit pixel width and height, principal point coordinates, etc. They represent the affine transformation from the pixel coordinate system to the image coordinate system and then to the camera coordinate system. Camera intrinsic parameters are generally calibrated using the Zhang Zhengyou calibration method.

![Zhang Zheng you](https://github.com/floriculture/Custom-your-own-3d-human-pose-dataset/blob/main/pic/pic9%20%E7%9B%B8%E6%9C%BA%E5%86%85%E5%8F%82%E6%A0%87%E5%AE%9A%E5%AE%9E%E6%99%AF.PNG)

The camera intrinsic parameters also include radial distortion and tangential distortion. In Human3.6M, we need to calibrate three tangential distortion coefficients and two radial distortion coefficients.

