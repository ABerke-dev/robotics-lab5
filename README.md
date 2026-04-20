# Robotics Lab 5: Camera Calibration and Stereovision

**Course:** Robotics I  
**Institution:** Poznan University of Technology, Institute of Robotics and Machine Intelligence

---

## **Laboratory Goals**

- Understand the fundamentals of **camera optics** and **lens distortion**.
- Learn **camera calibration** techniques using **OpenCV** and **ROS 2**.
- Explore **stereovision** principles and generate **disparity maps** for depth estimation.

---

## **Resources**

- [OpenCV: Camera Calibration Tutorial](https://docs.opencv.org/)
- [OpenCV: Stereo Vision Tutorial](https://docs.opencv.org/)
- [ROS 2: CameraInfo Message Definition](https://docs.ros.org/)
- [ROS 2: Camera Calibration Package Docs](https://docs.ros.org/)

---

## **Part I: Camera Optics and Calibration**

### **Why Camera Calibration?**

Every camera exhibits **radial** and **tangential distortion** due to lens imperfections. Calibration corrects these distortions and determines the camera's **intrinsic** (focal length, optical center) and **extrinsic** (rotation, translation) parameters.

### **Calibration Process**

1. Capture images of a known pattern (e.g., chessboard).
2. Detect pattern corners and compute the relationship between 3D points and 2D projections.
3. Obtain:
  - **Intrinsic Matrix (K):**
  - **Distortion Coefficients** (radial and tangential).
  - **Extrinsic Parameters** (rotation `R`, translation `T`).

---

## **Part II: Camera Calibration in ROS 2**

### **Steps**

1. **Run the Calibration Container:**
  ```bash
   docker run -it \
       --env="ROS_AUTOMATIC_DISCOVERY_RANGE=LOCALHOST" \
       --env="DISPLAY=$DISPLAY" \
       --env="QT_X11_NO_MITSHM=1" \
       --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
       --privileged --network=host \
       ros2_calibration:latest bash
  ```
2. **Build and Source:**
  ```bash
   colcon build --packages-select robotics_camera_calibration
   source install/setup.bash
  ```
3. **Run Calibration Node:**
  ```bash
   ros2 run camera_calibration cameracalibrator \
       -p chessboard --size 8x6 --square 0.065 \
       --no-service-check \
       --ros-args -r image:=/image_raw
  ```
4. **Play the Bag File:**
  ```bash
   ros2 bag play data/calibration_bag/
  ```
5. **Save Calibration Results:**
  - Click the **"Calibrate"** button.
  - Save the results to `/tmp/calibrationdata.tar.gz`.

---

## **Part III: Stereovision and Disparity Map**

### **Stereovision Setup**

- Uses **two cameras** to extract 3D information.
- **Disparity Map**: Encodes pixel differences between left/right images.
- **Depth Map**: Computed from the disparity map using calibration parameters.

### **Steps**

1. **Run Stereo Calibration:**
  ```bash
   ros2 run camera_calibration cameracalibrator \
       -p chessboard --size 8x6 --square 0.065 \
       --no-service-check \
       --ros-args -r left:=/left/image_raw -r right:=/right/image_raw
  ```
2. **Extract Calibration Results:**
  ```bash
   mkdir calibrationdata && tar xvf /tmp/calibrationdata.tar.gz -C calibrationdata
  ```
3. **Run Stereo Image Processing:**
  ```bash
   ros2 launch robotics_camera_calibration stereo_image_proc.launch.py
  ```
4. **Visualize Disparity Map:**
  ```bash
   ros2 run image_view stereo_view \
       _approximate_sync:=True \
       --ros-args -r /stereo/left/image:=/left/image_rect \
                   -r /stereo/right/image:=/right/image_rect \
                   -r /stereo/disparity:=/disparity
  ```
5. **Tune Parameters in Real-Time:**
  ```bash
   ros2 run rqt_reconfigure rqt_reconfigure --force-discover
  ```

---

## **Assignment Requirements**

To pass the course, upload a screenshot to **eCourses** showing:

- A **visible disparity map**.
- A **list of tuned parameters** in `rqt_reconfigure`.

---

## **Repository Contents**

- **Screenshots**: Calibration results, disparity map, and parameter tuning.
- **ROS 2 Bag Files**: Pre-recorded calibration and stereo images.
- **Calibration YAMLs**: Intrinsic/extrinsic parameters for left/right cameras.

---

## **How to Replicate This Lab**

1. Clone this repository:
  ```bash
   git clone https://github.com/ABerke-dev/robotics-lab5.git
  ```
2. Follow the steps in **Part II** and **Part III** to run calibration and stereovision.
3. Use the provided **Docker container** and **ROS 2 commands** for a seamless setup.

---

## **License**

This project is licensed under the **MIT License**.
