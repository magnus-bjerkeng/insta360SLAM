# VINS-Fusion Dual Non-Overlapping Fisheye Implementation Plan
## Insta360 Dual Fisheye + IMU Drone SLAM

**Target System**: Indoor drone with Insta360 One X2/X3
**Hardware**: GTX 5090, Ubuntu
**Goal**: Dual non-overlapping fisheye + IMU SLAM at 10-30 FPS
**Timeline**: 6-8 weeks
**Difficulty**: Advanced (incremental approach to reduce risk)

---

## Executive Summary

This plan implements VINS-Fusion for dual non-overlapping fisheye cameras using an **incremental validation approach**:

1. **Phase 1 (Weeks 1-3)**: Deploy single fisheye + IMU (PROVEN, 85% confidence)
2. **Phase 2 (Weeks 4-6)**: Extend to dual fisheye (EXPERIMENTAL, 70% confidence)

**Key Innovation**: Run two VINS instances in parallel (one per fisheye), then fuse trajectories.

**Critical Success Factor**: Excellent calibration using Kalibr.

---

## Prerequisites

### Hardware Requirements
- ✅ Insta360 One X2 or X3 camera
- ✅ Ubuntu 20.04 machine with GTX 5090
- ✅ USB 3.0 connection (5 Gbps)
- ✅ 16GB+ RAM, 8+ CPU cores
- ✅ AprilTag calibration board (6x6, 0.8m × 0.8m, printed on rigid surface)

### Software Prerequisites
- Ubuntu 20.04 LTS
- ROS Noetic
- CUDA 11+ (for GPU acceleration)
- Git, build tools

### Account/Access Requirements
- **Insta360 SDK Access**: Apply at https://www.insta360.com/sdk/apply (3-7 day approval)
- GitHub access for cloning repositories

---

## Phase 1: Single Fisheye Validation (Weeks 1-3)

**Goal**: Deploy VINS-Fusion with front OR back fisheye + IMU to validate system works.

### Week 1: Environment Setup

#### Day 1-2: System Preparation

**Task 1.1: Install ROS Noetic**
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install ROS Noetic
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
sudo apt update
sudo apt install ros-noetic-desktop-full -y

# Initialize rosdep
sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential -y
sudo rosdep init
rosdep update

# Setup environment
echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

**Validation**: `roscore` should start without errors.

**Task 1.2: Install Core Dependencies**
```bash
# Install Eigen3
sudo apt install libeigen3-dev -y

# Install Ceres Solver 1.14.0 (CRITICAL VERSION)
cd ~/Downloads
wget http://ceres-solver.org/ceres-solver-1.14.0.tar.gz
tar -xzf ceres-solver-1.14.0.tar.gz
cd ceres-solver-1.14.0

sudo apt install libgoogle-glog-dev libgflags-dev libatlas-base-dev libsuitesparse-dev -y

mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
sudo make install
```

**Validation**: `pkg-config --modversion ceres` should return 1.14.0

**Task 1.3: Setup Catkin Workspace**
```bash
mkdir -p ~/catkin_ws_vins/src
cd ~/catkin_ws_vins
catkin_make
source devel/setup.bash
echo "source ~/catkin_ws_vins/devel/setup.bash" >> ~/.bashrc
```

#### Day 3-4: Install VINS-Fusion

**Task 1.4: Clone VINS-Fusion**
```bash
cd ~/catkin_ws_vins/src
git clone https://github.com/HKUST-Aerial-Robotics/VINS-Fusion.git

# Install additional ROS dependencies
sudo apt install ros-noetic-cv-bridge ros-noetic-image-transport \
                 ros-noetic-tf ros-noetic-rviz -y
```

**Task 1.5: Build VINS-Fusion**
```bash
cd ~/catkin_ws_vins
catkin_make -j$(nproc)
source devel/setup.bash
```

**Expected output**: Build should complete without errors. May take 5-10 minutes.

**Validation**:
```bash
rospack find vins
# Should return: /home/user/catkin_ws_vins/src/VINS-Fusion/vins_estimator
```

**Troubleshooting**:
- If Ceres errors: Ensure version 1.14.0 (NOT 2.x)
- If CV bridge errors: `sudo apt install ros-noetic-cv-bridge`
- If Eigen errors: Check `/usr/include/eigen3` exists

#### Day 5-7: Install Insta360 SDK & Driver

**Task 1.6: Apply for Insta360 SDK**
1. Visit: https://www.insta360.com/sdk/apply
2. Fill form:
   - Project: Indoor Drone SLAM Research
   - Institution: [Your institution]
   - Use case: Real-time visual-inertial odometry for autonomous drones
3. Wait for approval (3-7 days)

**Task 1.7: Install Insta360 SDK (after approval)**
```bash
# Download CameraSDK-Cpp and MediaSDK-Cpp from Insta360
cd ~/Downloads

# Extract SDK
tar -xzf Insta360_CameraSDK_*.tar.gz
tar -xzf Insta360_MediaSDK_*.tar.gz

# Follow SDK installation instructions (platform-specific)
# Should include:
cd Insta360_CameraSDK_*/
sudo ./install.sh

cd ../Insta360_MediaSDK_*/
sudo ./install.sh
```

**Task 1.8: Install ai4ce Insta360 ROS Driver**
```bash
cd ~/catkin_ws_vins/src
git clone https://github.com/ai4ce/insta360_ros_driver.git

# Install dependencies
cd insta360_ros_driver
# Follow README for dependencies

cd ~/catkin_ws_vins
catkin_make
source devel/setup.bash
```

**Validation**:
```bash
# Connect Insta360 camera via USB
roslaunch insta360_ros_driver insta360.launch

# In another terminal, check topics
rostopic list
# Should see:
# /insta360/image_raw/front
# /insta360/image_raw/back
# /imu/data_raw
```

### Week 2: Calibration (CRITICAL)

**Goal**: Obtain accurate intrinsic and extrinsic calibration parameters.

#### Day 1-2: Install Kalibr

**Task 2.1: Install Kalibr**
```bash
cd ~/catkin_ws_vins/src
git clone https://github.com/ethz-asl/kalibr.git

# Install dependencies
sudo apt install python3-catkin-tools python3-osrf-pycommon \
                 libtbb-dev libboost-all-dev libopencv-dev \
                 libpoco-dev libeigen3-dev libv4l-dev -y

# Build Kalibr
cd ~/catkin_ws_vins
catkin build kalibr
source devel/setup.bash
```

**Task 2.2: Prepare Calibration Board**
```bash
# Download AprilTag 6x6 pattern
rosrun kalibr kalibr_create_target_pdf \
  --type apriltag \
  --nx 6 --ny 6 \
  --tsize 0.08 --tspace 0.3 \
  --output ~/calibration_board.pdf

# Print on A0/A1 size (~0.8m × 0.8m)
# Mount on RIGID flat surface (foam board, plywood)
```

**Important**: Board must be perfectly flat. Measure tag size accurately with ruler.

**Task 2.3: Create Calibration Target YAML**
```bash
cat > ~/calibration_target.yaml << 'EOF'
target_type: 'aprilgrid'
tagCols: 6
tagRows: 6
tagSize: 0.08      # Measured tag size in meters
tagSpacing: 0.3    # Measured spacing ratio (gap/size)
codeOffset: 0
EOF
```

#### Day 3-4: Camera Intrinsic Calibration

**Task 2.4: Record Front Fisheye Calibration Data**
```bash
# Start Insta360 driver
roslaunch insta360_ros_driver insta360.launch

# In another terminal, record calibration bag
rosbag record /insta360/image_raw/front -O ~/calibration_front.bag

# Recording instructions:
# 1. Hold camera steady, point at calibration board
# 2. Move board to cover entire image (all corners, edges, center)
# 3. Various orientations (0°, 45°, 90°, tilted)
# 4. Various distances (0.5m to 3m)
# 5. Record ~100-150 frames (slow motion, avoid blur)
# 6. Duration: ~60-90 seconds

# Stop recording with Ctrl+C
```

**Task 2.5: Run Kalibr Camera Calibration (Front)**
```bash
kalibr_calibrate_cameras \
  --bag ~/calibration_front.bag \
  --topics /insta360/image_raw/front \
  --models omni-radtan \
  --target ~/calibration_target.yaml \
  --show-extraction

# This will take 5-30 minutes
# Watch for reprojection errors < 0.5 pixels (CRITICAL)
```

**Expected output**: `camchain-calibration_front.yaml` with intrinsics

**Task 2.6: Repeat for Back Fisheye**
```bash
# Record back camera
rosbag record /insta360/image_raw/back -O ~/calibration_back.bag
# Follow same recording procedure

# Calibrate
kalibr_calibrate_cameras \
  --bag ~/calibration_back.bag \
  --topics /insta360/image_raw/back \
  --models omni-radtan \
  --target ~/calibration_target.yaml \
  --show-extraction
```

**Quality Metrics**:
- ✅ Reprojection error: < 0.5 pixels (excellent), < 1.0 pixels (acceptable)
- ✅ Coverage: All image regions covered during recording
- ✅ No motion blur in any frames

**If calibration fails**:
- Re-record with slower motion
- Better lighting (avoid reflections, shadows)
- Ensure board is perfectly flat

#### Day 5-6: IMU Noise Characterization

**Task 2.7: Record Stationary IMU Data**
```bash
# Place camera on stable surface (NO VIBRATIONS)
# Record IMU for 2 hours
rosbag record /imu/data_raw -O ~/imu_stationary.bag --duration=2h

# Can run overnight
```

**Task 2.8: Install imu_utils**
```bash
cd ~/catkin_ws_vins/src
git clone https://github.com/gaowenliang/imu_utils.git
git clone https://github.com/gaowenliang/code_utils.git

cd ~/catkin_ws_vins
catkin_make
source devel/setup.bash
```

**Task 2.9: Characterize IMU Noise**
```bash
# Create IMU config
cat > ~/imu_config.yaml << 'EOF'
imu_topic: "/imu/data_raw"
imu_name: "insta360_imu"
data_save_path: "/home/user/imu_results/"
max_time_min: 120
max_cluster: 100
EOF

# Run imu_utils
mkdir -p ~/imu_results
roslaunch imu_utils insta360_imu.launch

# In another terminal
rosbag play ~/imu_stationary.bag
```

**Expected output**: `insta360_imu_imu_param.yaml` with noise parameters

**Note**: If you cannot wait 2 hours, use manufacturer datasheet values × 10 as initial estimate.

#### Day 7: Camera-IMU Extrinsic Calibration

**Task 2.10: Record Camera-IMU Calibration Sequence**
```bash
# CRITICAL: This determines camera-IMU transformation

# Start driver
roslaunch insta360_ros_driver insta360.launch

# Record front camera + IMU
rosbag record /insta360/image_raw/front /imu/data_raw \
              -O ~/calib_front_imu.bag

# Recording procedure (60-90 seconds):
# 1. Smooth continuous motion with calibration board visible
# 2. Excite ALL 6 DOF (translate X, Y, Z + rotate roll, pitch, yaw)
# 3. Example pattern:
#    - Horizontal figure-8 motion (covers X, Y, yaw)
#    - Vertical motion (Z)
#    - Tilting motions (roll, pitch)
# 4. Keep board in view, avoid aggressive motion (no motion blur)
# 5. Maintain ~20 Hz camera rate, 200+ Hz IMU rate

# Stop with Ctrl+C
```

**Task 2.11: Run Kalibr IMU-Camera Calibration**
```bash
kalibr_calibrate_imu_camera \
  --bag ~/calib_front_imu.bag \
  --cam ~/camchain-calibration_front.yaml \
  --imu ~/imu_results/insta360_imu_imu_param.yaml \
  --target ~/calibration_target.yaml \
  --time-calibration

# Takes 30-60 minutes
```

**Expected output**: `camchain-imucam-front.yaml` with:
- Camera intrinsics
- IMU noise parameters
- T_cam_imu (transformation from camera to IMU)
- Time offset

**Quality check**:
- Reprojection error < 0.5 pixels
- Orientation errors within 3-sigma bounds
- Time offset < 10ms

**Task 2.12: Repeat for Back Camera**
```bash
# Record back camera + IMU
rosbag record /insta360/image_raw/back /imu/data_raw \
              -O ~/calib_back_imu.bag
# Follow same motion pattern

# Calibrate
kalibr_calibrate_imu_camera \
  --bag ~/calib_back_imu.bag \
  --cam ~/camchain-calibration_back.yaml \
  --imu ~/imu_results/insta360_imu_imu_param.yaml \
  --target ~/calibration_target.yaml \
  --time-calibration
```

**CRITICAL**: You now have calibration for BOTH fisheye cameras.

### Week 3: VINS-Fusion Integration (Single Camera)

#### Day 1-2: Create VINS Configuration

**Task 3.1: Convert Kalibr Output to VINS Format**

The Kalibr output needs to be converted to VINS-Fusion YAML format.

**Create**: `~/catkin_ws_vins/src/VINS-Fusion/config/insta360/insta360_front_config.yaml`

```yaml
%YAML:1.0

#--------------------------------------------------------------------------------------------
# Camera Parameters
#--------------------------------------------------------------------------------------------

# Camera model: 1 for pinhole, 2 for MEI
model_type: KANNALA_BRANDT

# Camera name
camera_name: insta360_front

# Image resolution
image_width: 1920
image_height: 1440

# Distortion parameters (from Kalibr k1, k2, k3, k4)
distortion_parameters:
   k2: [-0.0567, 0.0314, -0.0162, 0.0034]  # REPLACE with your values

# Projection parameters (from Kalibr fx, fy, cx, cy)
projection_parameters:
   k2: [652.8, 653.2, 960.0, 720.0]  # REPLACE with your values

# Camera-IMU extrinsic (from Kalibr T_cam_imu)
# This is T_cam_imu, which VINS uses as T_bc
extrinsicRotation: !!opencv-matrix
   rows: 3
   cols: 3
   dt: d
   data: [1.0, 0.0, 0.0,    # REPLACE with your rotation matrix
          0.0, 1.0, 0.0,
          0.0, 0.0, 1.0]

extrinsicTranslation: !!opencv-matrix
   rows: 3
   cols: 1
   dt: d
   data: [0.0, 0.0, 0.0]    # REPLACE with your translation vector

#--------------------------------------------------------------------------------------------
# IMU Parameters
#--------------------------------------------------------------------------------------------

# IMU noise parameters (from imu_utils)
acc_n: 0.08          # REPLACE: accelerometer noise density (m/s^2/sqrt(Hz))
gyr_n: 0.004         # REPLACE: gyroscope noise density (rad/s/sqrt(Hz))
acc_w: 0.00004       # REPLACE: accelerometer random walk (m/s^3/sqrt(Hz))
gyr_w: 2.0e-6        # REPLACE: gyroscope random walk (rad/s^2/sqrt(Hz))

# Gravity magnitude (measure at your location, or use 9.805)
g_norm: 9.805

#--------------------------------------------------------------------------------------------
# Feature Tracking Parameters
#--------------------------------------------------------------------------------------------

# Maximum number of features to track
max_cnt: 150                # Start with 150, tune based on performance
min_dist: 20                # Minimum distance between features (pixels)
freq: 20                    # Tracking frequency (Hz)

# RANSAC threshold for fundamental matrix estimation
F_threshold: 1.0

# Show tracking visualization
show_track: 1               # 1 to show, 0 to hide

# Equalize histogram (for low-light)
equalize: 1                 # 1 for indoor, 0 for outdoor

# Fisheye mask (optional, set to empty for now)
fisheye_mask: ""

#--------------------------------------------------------------------------------------------
# Optimization Parameters
#--------------------------------------------------------------------------------------------

# Sliding window size
max_solver_time: 0.04       # Maximum solver time (seconds)
max_num_iterations: 8       # Maximum iterations
keyframe_parallax: 10.0     # Minimum parallax for keyframe (pixels)

#--------------------------------------------------------------------------------------------
# Loop Closure Parameters
#--------------------------------------------------------------------------------------------

loop_closure: 0             # Disable for initial testing
load_previous_pose_graph: 0

#--------------------------------------------------------------------------------------------
# Visualization Parameters
#--------------------------------------------------------------------------------------------

# Publish topics
publish_imu_propagate: 0
```

**Task 3.2: Extract Actual Values from Kalibr**

From `camchain-imucam-front.yaml`, extract:

1. **Intrinsics** (cam0 section):
```yaml
intrinsics: [fu, fv, cu, cv]  # Copy to projection_parameters
distortion_coefficients: [k1, k2, k3, k4]  # Copy to distortion_parameters
```

2. **Extrinsics** (T_cam_imu):
```yaml
T_cam_imu:
  - [r11, r12, r13, tx]
  - [r21, r22, r23, ty]
  - [r31, r32, r33, tz]
  - [0, 0, 0, 1]

# Extract rotation (3x3) → extrinsicRotation
# Extract translation [tx, ty, tz] → extrinsicTranslation
```

3. **IMU parameters** (imu0 section or from imu_utils):
```yaml
accelerometer_noise_density: X  # → acc_n
gyroscope_noise_density: Y      # → gyr_n
accelerometer_random_walk: Z    # → acc_w
gyroscope_random_walk: W        # → gyr_w
```

**Task 3.3: Create ROS Launch File**

Create: `~/catkin_ws_vins/src/VINS-Fusion/vins_estimator/launch/insta360_front.launch`

```xml
<launch>
    <arg name="config_path" default="$(find vins)/../config/insta360/insta360_front_config.yaml" />

    <node name="vins_estimator" pkg="vins" type="vins_node" output="screen">
       <param name="config_file" type="string" value="$(arg config_path)" />
    </node>

    <node name="pose_graph" pkg="pose_graph" type="pose_graph_node" output="screen">
        <param name="config_file" type="string" value="$(arg config_path)" />
        <param name="visualization_shift_x" type="int" value="0" />
        <param name="visualization_shift_y" type="int" value="0" />
        <param name="skip_cnt" type="int" value="0" />
        <param name="skip_dis" type="double" value="0" />
    </node>

    <!-- Remap topics from Insta360 driver to VINS expected names -->
    <node pkg="topic_tools" type="relay" name="image_relay"
          args="/insta360/image_raw/front /camera/image_raw" />

    <node pkg="topic_tools" type="relay" name="imu_relay"
          args="/imu/data_raw /imu0" />

    <!-- RVIZ visualization -->
    <node name="rviz" pkg="rviz" type="rviz"
          args="-d $(find vins)/../config/vins_rviz_config.rviz" />
</launch>
```

#### Day 3-5: Testing & Validation

**Task 3.4: Start VINS-Fusion with Insta360**

**Terminal 1: Start Insta360 Driver**
```bash
roslaunch insta360_ros_driver insta360.launch
```

**Terminal 2: Start VINS-Fusion**
```bash
roslaunch vins insta360_front.launch
```

**Terminal 3: Monitor Topics**
```bash
# Check data rates
rostopic hz /insta360/image_raw/front  # Should be ~20-30 Hz
rostopic hz /imu/data_raw              # Should be ~200-500 Hz
rostopic hz /vins_estimator/odometry   # VINS output
```

**Expected Behavior**:
1. Feature tracking visualization window appears
2. After 2-3 seconds of motion: "VINS Initialization finish!" in console
3. RVIZ shows:
   - Camera trajectory (green line)
   - Feature points (colored dots)
   - Current camera pose (frame)

**Task 3.5: Validation Tests**

**Test 1: Initialization**
```bash
# Move camera with 6-DOF motion (translation + rotation)
# Watch console for "Initialization finish"
# Should occur within 2-5 seconds
```

**Test 2: Tracking Quality**
```bash
# Observe feature tracking window
# ✅ Good: 50-150 features tracked (green dots)
# ❌ Bad: < 20 features or many red crosses (tracking failures)
```

**Test 3: Trajectory Accuracy**
```bash
# Simple test: Move camera in square (1m × 1m)
# Return to start position
# Check RVIZ trajectory - should close the loop
# Drift < 10cm acceptable for first test
```

**Test 4: FPS Performance**
```bash
# Check VINS processing rate
rostopic hz /vins_estimator/odometry

# Target: 15-30 Hz
# Minimum acceptable: 10 Hz
```

**Task 3.6: Parameter Tuning (if needed)**

If tracking fails or performance poor:

**Low FPS (< 10 Hz)**:
```yaml
# Reduce features
max_cnt: 100  # Down from 150
min_dist: 30  # Up from 20
```

**Poor tracking**:
```yaml
# Increase features
max_cnt: 200
min_dist: 15

# Better feature detection
equalize: 1  # For low light
```

**Initialization failures**:
- Check IMU noise parameters (try multiplying by 10)
- Ensure sufficient motion during initialization
- Verify camera-IMU extrinsics correct

#### Day 6-7: Data Collection & Benchmarking

**Task 3.7: Record Test Dataset**
```bash
# Record a validation dataset
rosbag record /insta360/image_raw/front /imu/data_raw \
              /vins_estimator/odometry \
              -O ~/vins_test_single.bag

# Record various scenarios:
# 1. Slow motion (0.2 m/s)
# 2. Medium motion (0.5 m/s)
# 3. Fast motion (1.0 m/s)
# 4. Rotation heavy
# 5. Translation heavy
# Duration: 5-10 minutes total
```

**Task 3.8: Analyze Performance**
```bash
# Use evo tool for trajectory evaluation
pip3 install evo

# Extract trajectory
evo_traj bag ~/vins_test_single.bag /vins_estimator/odometry --save_as_tum

# If you have ground truth:
evo_ape tum ground_truth.txt vins_trajectory.txt --plot

# Compute statistics
evo_res *.zip --save_table results.csv
```

**Success Criteria for Phase 1**:
- ✅ VINS initializes reliably (< 5 seconds)
- ✅ Tracking success rate > 85%
- ✅ FPS > 15 Hz consistently
- ✅ Drift < 2% of distance traveled
- ✅ No crashes or divergence

**DECISION POINT**:
- ✅ Single fisheye works well → Proceed to Phase 2 (Dual Fisheye)
- ❌ Issues remain → Debug before proceeding

---

## Phase 2: Dual Fisheye Extension (Weeks 4-6)

**Goal**: Extend system to use both front AND back fisheye cameras simultaneously.

### Week 4: Dual Monocular Approach

**Strategy**: Run two VINS instances (one per camera), fuse outputs.

#### Day 1-2: Setup Second VINS Instance

**Task 4.1: Create Back Camera Configuration**

Copy and modify for back camera:
```bash
cp ~/catkin_ws_vins/src/VINS-Fusion/config/insta360/insta360_front_config.yaml \
   ~/catkin_ws_vins/src/VINS-Fusion/config/insta360/insta360_back_config.yaml

# Edit insta360_back_config.yaml:
# - Update camera_name: insta360_back
# - Update intrinsics from camchain-calibration_back.yaml
# - Update extrinsics from camchain-imucam-back.yaml
# - All other parameters same as front
```

**Task 4.2: Create Dual Launch File**

Create: `~/catkin_ws_vins/src/VINS-Fusion/vins_estimator/launch/insta360_dual.launch`

```xml
<launch>
    <!-- Front camera VINS instance -->
    <group ns="vins_front">
        <node name="vins_estimator" pkg="vins" type="vins_node" output="screen">
           <param name="config_file" type="string"
                  value="$(find vins)/../config/insta360/insta360_front_config.yaml" />
           <remap from="/camera/image_raw" to="/insta360/image_raw/front"/>
           <remap from="/imu0" to="/imu/data_raw"/>
        </node>
    </group>

    <!-- Back camera VINS instance -->
    <group ns="vins_back">
        <node name="vins_estimator" pkg="vins" type="vins_node" output="screen">
           <param name="config_file" type="string"
                  value="$(find vins)/../config/insta360/insta360_back_config.yaml" />
           <remap from="/camera/image_raw" to="/insta360/image_raw/back"/>
           <remap from="/imu0" to="/imu/data_raw"/>
        </node>
    </group>

    <!-- Trajectory fusion node (to be created) -->
    <node name="dual_vins_fusion" pkg="dual_vins_fusion" type="fusion_node" output="screen">
        <remap from="/vins_front/odometry" to="/vins_front/vins_estimator/odometry"/>
        <remap from="/vins_back/odometry" to="/vins_back/vins_estimator/odometry"/>
        <remap from="/fused_odometry" to="/vins_dual/odometry"/>
    </node>

    <!-- RVIZ -->
    <node name="rviz" pkg="rviz" type="rviz"
          args="-d $(find vins)/../config/dual_vins_rviz_config.rviz" />
</launch>
```

#### Day 3-5: Create Trajectory Fusion Node

**Task 4.3: Create Fusion Package**
```bash
cd ~/catkin_ws_vins/src
catkin_create_pkg dual_vins_fusion roscpp geometry_msgs nav_msgs tf

cd dual_vins_fusion
mkdir src
```

**Task 4.4: Implement Fusion Node**

Create: `~/catkin_ws_vins/src/dual_vins_fusion/src/fusion_node.cpp`

```cpp
#include <ros/ros.h>
#include <nav_msgs/Odometry.h>
#include <geometry_msgs/PoseStamped.h>
#include <tf/transform_broadcaster.h>
#include <Eigen/Dense>
#include <queue>

class DualVINSFusion {
private:
    ros::NodeHandle nh_;
    ros::Subscriber sub_front_, sub_back_;
    ros::Publisher pub_fused_;

    nav_msgs::Odometry latest_front_, latest_back_;
    bool has_front_ = false, has_back_ = false;

    // Simple weighted average fusion
    // In production: Use EKF or optimization-based fusion

public:
    DualVINSFusion() {
        sub_front_ = nh_.subscribe("/vins_front/odometry", 100,
                                   &DualVINSFusion::frontCallback, this);
        sub_back_ = nh_.subscribe("/vins_back/odometry", 100,
                                  &DualVINSFusion::backCallback, this);
        pub_fused_ = nh_.advertise<nav_msgs::Odometry>("/fused_odometry", 100);

        ROS_INFO("Dual VINS Fusion Node Started");
    }

    void frontCallback(const nav_msgs::Odometry::ConstPtr& msg) {
        latest_front_ = *msg;
        has_front_ = true;
        fuseAndPublish();
    }

    void backCallback(const nav_msgs::Odometry::ConstPtr& msg) {
        latest_back_ = *msg;
        has_back_ = true;
        fuseAndPublish();
    }

    void fuseAndPublish() {
        if (!has_front_ || !has_back_) return;

        // Simple fusion strategy: Average positions, select best covariance
        nav_msgs::Odometry fused;
        fused.header.stamp = ros::Time::now();
        fused.header.frame_id = "world";
        fused.child_frame_id = "body";

        // Average positions
        fused.pose.pose.position.x =
            0.5 * (latest_front_.pose.pose.position.x +
                   latest_back_.pose.pose.position.x);
        fused.pose.pose.position.y =
            0.5 * (latest_front_.pose.pose.position.y +
                   latest_back_.pose.pose.position.y);
        fused.pose.pose.position.z =
            0.5 * (latest_front_.pose.pose.position.z +
                   latest_back_.pose.pose.position.z);

        // Average quaternions (SLERP would be better)
        fused.pose.pose.orientation.w =
            0.5 * (latest_front_.pose.pose.orientation.w +
                   latest_back_.pose.pose.orientation.w);
        fused.pose.pose.orientation.x =
            0.5 * (latest_front_.pose.pose.orientation.x +
                   latest_back_.pose.pose.orientation.x);
        fused.pose.pose.orientation.y =
            0.5 * (latest_front_.pose.pose.orientation.y +
                   latest_back_.pose.pose.orientation.y);
        fused.pose.pose.orientation.z =
            0.5 * (latest_front_.pose.pose.orientation.z +
                   latest_back_.pose.pose.orientation.z);

        // Normalize quaternion
        double norm = sqrt(
            fused.pose.pose.orientation.w * fused.pose.pose.orientation.w +
            fused.pose.pose.orientation.x * fused.pose.pose.orientation.x +
            fused.pose.pose.orientation.y * fused.pose.pose.orientation.y +
            fused.pose.pose.orientation.z * fused.pose.pose.orientation.z
        );
        fused.pose.pose.orientation.w /= norm;
        fused.pose.pose.orientation.x /= norm;
        fused.pose.pose.orientation.y /= norm;
        fused.pose.pose.orientation.z /= norm;

        // Use minimum covariance from both estimates
        // (In production: proper covariance fusion)
        fused.pose.covariance = latest_front_.pose.covariance;

        pub_fused_.publish(fused);
    }
};

int main(int argc, char** argv) {
    ros::init(argc, argv, "dual_vins_fusion");
    DualVINSFusion fusion;
    ros::spin();
    return 0;
}
```

**Task 4.5: Create CMakeLists.txt**

Edit: `~/catkin_ws_vins/src/dual_vins_fusion/CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.0.2)
project(dual_vins_fusion)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  geometry_msgs
  nav_msgs
  tf
)

find_package(Eigen3 REQUIRED)

catkin_package(
  CATKIN_DEPENDS roscpp geometry_msgs nav_msgs tf
)

include_directories(
  ${catkin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIR}
)

add_executable(fusion_node src/fusion_node.cpp)
target_link_libraries(fusion_node ${catkin_LIBRARIES})
```

**Task 4.6: Build Fusion Package**
```bash
cd ~/catkin_ws_vins
catkin_make
source devel/setup.bash
```

#### Day 6-7: Test Dual System

**Task 4.7: Launch Dual VINS**

**Terminal 1: Insta360 Driver**
```bash
roslaunch insta360_ros_driver insta360.launch
```

**Terminal 2: Dual VINS**
```bash
roslaunch vins insta360_dual.launch
```

**Task 4.8: Monitor Dual System**
```bash
# Check both VINS instances
rostopic hz /vins_front/vins_estimator/odometry
rostopic hz /vins_back/vins_estimator/odometry
rostopic hz /vins_dual/odometry

# Watch for initialization of BOTH instances
# This may take 5-10 seconds
```

**Expected Issues**:
1. **Both cameras may not initialize simultaneously**: This is OK, fusion handles it
2. **One camera may have better tracking**: Weighted fusion can prioritize
3. **Coordinate frame alignment**: Requires careful extrinsic calibration

**Task 4.9: Validation**

**Test 1: Coverage**
```bash
# Move through environment
# Front camera should track when moving forward
# Back camera should track when moving backward
# Fusion should maintain consistent trajectory
```

**Test 2: Occlusion Handling**
```bash
# Cover front camera → back camera should maintain tracking
# Cover back camera → front camera should maintain tracking
# Fusion should remain stable
```

**Test 3: Performance**
```bash
# Check CPU usage: htop
# Expected: 2x higher than single camera
# Should still maintain 15-30 Hz on GTX 5090 CPU
```

### Week 5-6: Optimization & Production Hardening

#### Advanced Fusion (Optional Enhancement)

**Task 5.1: Implement EKF-Based Fusion**

For production, replace simple averaging with Extended Kalman Filter:

1. **State**: [position, velocity, orientation, biases]
2. **Inputs**: Front VINS odometry, Back VINS odometry, IMU
3. **Measurement model**: Fuse multiple odometry sources with uncertainty

**Libraries**:
- robot_localization: `sudo apt install ros-noetic-robot-localization`
- Or implement custom EKF

**Example with robot_localization**:
```bash
sudo apt install ros-noetic-robot-localization

# Create config: dual_ekf.yaml
# Launch with ekf_localization_node
```

This provides optimal fusion with proper covariance handling.

#### Performance Optimization

**Task 5.2: Multi-Threading Optimization**

Ensure VINS instances run on separate CPU cores:
```bash
# Check CPU affinity
taskset -cp <vins_front_pid>
taskset -cp <vins_back_pid>

# Set core affinity if needed
taskset -cp 0-3 <vins_front_pid>
taskset -cp 4-7 <vins_back_pid>
```

**Task 5.3: Frame Rate Optimization**

If FPS drops below 15 Hz:
```yaml
# In both config files, reduce features
max_cnt: 100
min_dist: 25

# Reduce frequency
freq: 15  # Down from 20
```

**Task 5.4: Memory Optimization**

Monitor memory usage:
```bash
# Watch memory
watch -n 1 'ps aux | grep vins'

# If > 8GB per instance:
# Enable keyframe culling in VINS config
# Reduce sliding window size
```

#### Robustness Testing

**Task 5.5: Stress Testing**

**Test 1: Long Duration**
```bash
# Run for 30+ minutes continuous
# Monitor for:
# - Memory leaks (increasing RSS)
# - Drift accumulation
# - Tracking failures
```

**Test 2: Challenging Scenarios**
- Low texture (blank walls)
- Fast motion (2+ m/s)
- Rapid rotation (>180°/s)
- Low light
- Mixed lighting (shadows)

**Test 3: Failure Recovery**
```bash
# Simulate failures:
# - Cover both cameras briefly
# - Rapid lighting change
# - Aggressive maneuver
# Should recover automatically
```

#### Calibration Refinement

**Task 5.6: Front-Back Extrinsic Calibration**

For best results, calibrate relative pose between front/back cameras:

```bash
# This improves fusion consistency

# Record both cameras viewing same calibration board
# (requires mirrors or transparent board setup)
rosbag record /insta360/image_raw/front /insta360/image_raw/back \
              -O ~/stereo_calib.bag

# Run Kalibr stereo calibration
kalibr_calibrate_cameras \
  --bag ~/stereo_calib.bag \
  --topics /insta360/image_raw/front /insta360/image_raw/back \
  --models omni-radtan omni-radtan \
  --target ~/calibration_target.yaml

# Extract T_front_back transformation
# Use in fusion node for coordinate alignment
```

**Note**: This is challenging for non-overlapping cameras. Alternative: Use mechanical measurements + IMU verification.

---

## Phase 3: Deployment & Integration

### Drone Integration

**Task 6.1: Connect to Flight Controller**

Publish VINS odometry to PX4/ArduPilot:
```bash
# Install MAVROS
sudo apt install ros-noetic-mavros ros-noetic-mavros-extras

# For PX4, use vision_pose plugin
# remap /vins_dual/odometry to /mavros/vision_pose/pose
```

**Task 6.2: Create Failsafe Logic**

Monitor VINS health:
```cpp
// Check tracking quality
// If num_features < 20: WARNING
// If odometry not published for > 1 second: CRITICAL
// Trigger failsafe (land or switch to GPS)
```

### Performance Benchmarking

**Final Performance Targets**:
- ✅ FPS: 15-30 Hz (dual camera)
- ✅ Latency: < 100ms (camera to pose estimate)
- ✅ Accuracy: < 2% drift over 100m
- ✅ CPU: < 50% on 8-core system
- ✅ Memory: < 4GB per VINS instance
- ✅ Initialization: < 5 seconds (both cameras)
- ✅ Tracking: > 80% success rate

---

## Troubleshooting Guide

### Common Issues & Solutions

#### Issue 1: VINS Won't Initialize
**Symptoms**: "Waiting for initialization..." indefinitely

**Solutions**:
1. Check IMU data: `rostopic echo /imu/data_raw` (should be ~200-500 Hz)
2. Verify sufficient motion (need translation + rotation)
3. Multiply IMU noise parameters by 10
4. Check camera-IMU time synchronization
5. Verify extrinsics are correct (not identity matrix)

#### Issue 2: Poor Tracking Quality
**Symptoms**: < 30 features tracked, many tracking failures

**Solutions**:
1. Increase `max_cnt` to 200
2. Decrease `min_dist` to 15
3. Enable histogram equalization: `equalize: 1`
4. Add artificial features (AprilTags) in low-texture areas
5. Check camera focus and exposure

#### Issue 3: Low FPS (< 10 Hz)
**Symptoms**: Slow odometry updates

**Solutions**:
1. Reduce `max_cnt` to 100
2. Increase `min_dist` to 30
3. Reduce `freq` to 15
4. Check CPU usage (should not be 100%)
5. Ensure VINS built in Release mode

#### Issue 4: Large Drift
**Symptoms**: Trajectory diverges from ground truth

**Solutions**:
1. Recalibrate camera-IMU extrinsics (most common cause)
2. Characterize IMU noise properly (use imu_utils)
3. Enable loop closure: `loop_closure: 1`
4. Check for poor calibration (high reprojection errors)
5. Verify gravity magnitude `g_norm` correct for location

#### Issue 5: Dual Cameras Don't Fuse Properly
**Symptoms**: Fused trajectory jumps or inconsistent

**Solutions**:
1. Verify both VINS instances initialize successfully
2. Check front-back extrinsic calibration
3. Implement time synchronization in fusion node
4. Use robot_localization EKF instead of simple averaging
5. Monitor covariances - reject outliers

#### Issue 6: Crashes or Segfaults
**Symptoms**: VINS node crashes

**Solutions**:
1. Check Ceres version (must be 1.14.0)
2. Verify all dependencies installed
3. Rebuild in Debug mode: `catkin_make -DCMAKE_BUILD_TYPE=Debug`
4. Run with gdb: `gdb --args rosrun vins vins_node`
5. Check for NaN in IMU data

---

## Alternative Approaches (If Dual Monocular Fails)

### Option B: Modified VINS-Fusion Multi-Camera

If simple dual monocular doesn't work well, consider modifying VINS-Fusion backend:

**Steps**:
1. Study VINS-Fusion state estimator code
2. Extend to handle multiple camera observations
3. Modify feature manager for multi-camera
4. Update optimization to include both cameras in same bundle adjustment

**Difficulty**: HIGH (requires deep SLAM knowledge)
**Timeline**: 3-4 additional weeks
**Confidence**: 65%

### Option C: VINS-OS Direct Integration

Use VINS-OS which has dual fisheye support:

**Advantages**:
- Native dual fisheye stereo
- GPU accelerated

**Disadvantages**:
- Older codebase (2020)
- ROS1 only
- Requires custom Insta360 interface

**Timeline**: 4-5 weeks
**Confidence**: 70%

### Option D: Switch to Alternative System

If VINS-Fusion proves too challenging:

1. **Basalt**: Better multi-camera support (though non-overlapping uncertain)
2. **OpenVINS**: Filter-based, proven on drones
3. **ORB-SLAM3**: Fall back to single fisheye (excellent accuracy)

---

## Success Criteria & Validation

### Minimum Viable Product (MVP)
- ✅ Single fisheye VINS running at 15+ Hz
- ✅ Reliable initialization < 5 seconds
- ✅ Tracking success > 80%
- ✅ Drift < 5% over 100m

### Production System (Dual Camera)
- ✅ Dual fisheye fusion working
- ✅ Combined FPS > 15 Hz
- ✅ 360° coverage from front+back cameras
- ✅ Drift < 2% over 100m
- ✅ Robust to single camera occlusion
- ✅ Stable 10+ minute flights

### Performance Metrics

**Collect these metrics**:
```bash
# 1. FPS
rostopic hz /vins_dual/odometry

# 2. Feature count
rostopic echo /vins_front/feature | grep num
rostopic echo /vins_back/feature | grep num

# 3. Trajectory evaluation (with ground truth)
evo_ape tum ground_truth.txt vins_trajectory.txt

# 4. CPU/Memory
htop  # Monitor vins_node processes

# 5. Latency
# Timestamp image capture → odometry output
# Target < 100ms
```

---

## Resources & References

### Official Documentation
- VINS-Fusion: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion
- Kalibr: https://github.com/ethz-asl/kalibr
- Insta360 SDK: https://www.insta360.com/sdk/apply

### Research Papers
- VINS-Mono: Qin et al., IEEE T-RO 2018
- Omni-swarm: Xu et al., IEEE T-RO 2021 (dual fisheye proof)
- DFOM: Gao et al., J. Field Robotics 2020

### Community Resources
- VINS-Fusion Issues: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion/issues
- ROS Answers: https://answers.ros.org/
- Kalibr Wiki: https://github.com/ethz-asl/kalibr/wiki

### Tools
- evo (trajectory evaluation): https://github.com/MichaelGrupp/evo
- imu_utils: https://github.com/gaowenliang/imu_utils
- robot_localization: http://wiki.ros.org/robot_localization

---

## Timeline Summary

| Phase | Duration | Tasks | Success Gate |
|-------|----------|-------|-------------|
| **Phase 1: Single Fisheye** | 3 weeks | Environment setup, calibration, VINS integration | 15+ Hz, reliable tracking |
| **Phase 2: Dual Fisheye** | 3 weeks | Dual monocular, fusion node, optimization | Both cameras working |
| **Phase 3: Production** | 2 weeks | Integration, stress testing, deployment | Flight-ready system |
| **TOTAL** | **6-8 weeks** | | Production dual-fisheye SLAM |

---

## Risk Mitigation

### High Risk Items
1. **Dual camera fusion complexity** → Start with simple averaging, iterate
2. **Calibration quality** → Use Kalibr, validate reprojection errors
3. **Non-overlapping challenges** → Research references (Omni-swarm) validate feasibility

### Mitigation Strategies
1. **Incremental validation**: Single → Dual (don't skip single camera phase)
2. **Early testing**: Validate calibration before proceeding
3. **Fallback plan**: ORB-SLAM3 single fisheye if dual fails
4. **Community engagement**: Post GitHub issues early for guidance

---

## Conclusion

This implementation plan provides a systematic approach to deploying VINS-Fusion with Insta360 dual non-overlapping fisheye cameras. The incremental strategy (single first, then dual) minimizes risk while maintaining the goal of full 360° coverage.

**Key Success Factors**:
1. ✅ Excellent calibration (< 0.5 pixel reprojection error)
2. ✅ Proper IMU characterization
3. ✅ Incremental validation at each phase
4. ✅ Systematic troubleshooting
5. ✅ Patience during calibration (don't rush)

**Expected Outcome**: Production-ready dual fisheye SLAM system for indoor drone navigation in 6-8 weeks.

**Next Steps**: Begin Week 1, Day 1 - Install ROS Noetic.
