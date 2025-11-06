# VINS-Fusion vs Stella VSLAM: Direct Comparison
## For Insta360 Dual Non-Overlapping Fisheye + IMU Drone

**Date**: 2025-11-06
**Setup**: Indoor drone, Insta360 One X2/X3, GTX 5090, Ubuntu
**Requirements**: Dual fisheye, IMU fusion, 1-30 FPS minimum

---

## Executive Summary

### The Critical Difference: IMU Integration

**VINS-Fusion**: ✅ Tightly-coupled visual-inertial SLAM (IMU is core feature)
**Stella VSLAM**: ❌ NO native IMU support (visual-only, IMU on roadmap but NOT implemented)

**For a drone, this is a SHOWSTOPPER difference.**

### Quick Verdict

| Aspect | VINS-Fusion | Stella VSLAM | Winner |
|--------|-------------|--------------|---------|
| **IMU Integration** | ✅ Tightly-coupled | ❌ None | **VINS-Fusion** |
| **Drone Suitability** | ✅✅ Purpose-built | ❌ Visual-only risky | **VINS-Fusion** |
| **Dual Fisheye** | ⚠️ Extension needed | ❌ Needs stitching | **Tie** |
| **Insta360 Support** | Generic fisheye | ✅ Explicit (ONE X2) | **Stella** |
| **Setup Complexity** | Higher (ROS, calib) | Lower (Docker) | **Stella** |
| **Performance** | 25-30 FPS | 20-30 FPS | **Tie** |
| **Accuracy** | 2-5 cm (with IMU) | 2-3 cm (drift worse) | **VINS-Fusion** |
| **Community** | 4,200 stars | 1,100 stars | **VINS-Fusion** |

**RECOMMENDATION**: **VINS-Fusion** - IMU integration is non-negotiable for drones.

---

## Detailed Comparison

### 1. IMU Integration ⭐ CRITICAL FOR DRONES

#### VINS-Fusion: Tightly-Coupled Visual-Inertial ✅

**Implementation**:
- Joint optimization of visual features + IMU measurements
- IMU preintegration between keyframes
- Visual-inertial bundle adjustment
- Proven with 6-axis IMU @ 200-400 Hz

**Benefits for Drones**:
```
✅ Handles fast aggressive maneuvers
✅ Scale observability (metric scale recovered)
✅ Bridges visual occlusions (IMU keeps tracking during feature loss)
✅ Rotation accuracy (IMU provides gravity direction)
✅ Faster initialization (5-10x faster convergence)
✅ Drift reduction (50-80% reduction in trajectory error)
✅ Critical for safety (maintains state during visual failures)
```

**Configuration**:
```yaml
# From VINS config
acc_n: 0.08          # Accelerometer noise density
gyr_n: 0.004         # Gyroscope noise density
acc_w: 0.00004       # Accelerometer random walk
gyr_w: 2.0e-6        # Gyroscope random walk
g_norm: 9.805        # Gravity magnitude
```

**Proven Performance**: 2-5 cm accuracy with IMU vs 5-10+ cm visual-only

---

#### Stella VSLAM: NO IMU Support ❌

**Current Status**:
- Visual-only SLAM (ORB features + tracking)
- IMU integration listed as priority #7 in roadmap
- **NOT implemented** as of 2024
- No timeline provided by maintainers

**Report Quote**:
> "Stella VSLAM is visual-only SLAM. IMU integration is on the roadmap (priority #7 in README) but NOT yet implemented."

**Workarounds Suggested** (all suboptimal):

**Option A: External Loose Coupling**
- Implement separate IMU preintegration (e.g., GTSAM)
- Fuse Stella VSLAM pose estimates with IMU data offline/online
- Lower integration effort (1-2 weeks)
- NOT as accurate as tightly-coupled

**Option B: Fork for Tight Coupling** (NOT RECOMMENDED)
- Fork stella_vslam, integrate IMU preintegration yourself
- Add IMU-visual bundle adjustment
- High effort: **4-8 weeks of development**
- Requires deep SLAM expertise

**Option C: Switch to VI-SLAM** (what we're doing!)
- Use ORB-SLAM3, VINS-Fusion, or Basalt instead
- Get proper IMU integration

---

**Why IMU is CRITICAL for Drones**:

Indoor drones experience:
- **Fast motions**: 1-3 m/s translation, 90-180°/s rotation
- **Aggressive maneuvers**: Sudden stops, direction changes
- **Visual failures**: Motion blur, low texture, lighting changes
- **Safety requirements**: Must maintain state estimate for emergency landing

**Without IMU**:
```
❌ Pure rotation = scale ambiguity (can't distinguish fast rotation from translation)
❌ Motion blur = tracking failures → complete loss of state
❌ Feature-poor areas = drift accumulation
❌ Fast motion = increased tracking failures
❌ No fallback during visual occlusion
```

**With IMU (VINS-Fusion)**:
```
✅ IMU provides orientation even when camera fails
✅ Scale is observable from gravity + accelerometer
✅ Short-term accurate state during visual failures (IMU propagation)
✅ Can detect and recover from tracking loss
✅ Emergency landing possible with IMU-only state
```

**VERDICT**: **VINS-Fusion wins decisively** - IMU is non-negotiable for drone safety.

---

### 2. Dual Non-Overlapping Fisheye Support

#### VINS-Fusion: Extension Required ⚠️

**Native Support**: Single fisheye + IMU (monocular-inertial) or stereo fisheye (overlapping)

**For Dual Non-Overlapping**:
```
❌ NOT natively supported
✅ Extension feasible (proven in research)
```

**Research Evidence**:
- **Omni-swarm** (IEEE T-RO 2021): Dual fisheye on aerial swarms, centimeter accuracy
- **DFOM** (J. Field Robotics 2020): Dual fisheye omnidirectional, real-time onboard
- Both from HKUST Aerial Robotics (same team as VINS-Fusion)

**Extension Approach**:

**Option A: Dual Monocular (Recommended)**
- Run TWO VINS instances (one per camera)
- Fuse trajectories in separate node
- Effort: **1-2 weeks**
- Confidence: **70%**

**Option B: Full Multi-Camera Extension**
- Modify VINS state estimator for joint optimization
- Both cameras in same bundle adjustment
- Effort: **3-4 weeks**
- Confidence: **65%**
- Higher accuracy potential

**Timeline**: 6-8 weeks total (3 weeks single + 3 weeks dual extension)

---

#### Stella VSLAM: Stitching Required ❌

**Native Support**: Equirectangular (360°) camera model

**For Dual Fisheye**:
```
❌ Cannot use dual fisheye directly
❌ Must stitch to equirectangular FIRST
✅ Equirectangular mode proven with Insta360
```

**Stitching Pipeline**:
```
Insta360 Front Fisheye (195° FOV)
        +
Insta360 Back Fisheye (195° FOV)
        ↓
[Real-time Stitching: Insta360 SDK GPU OR OpenCV]
        ↓
Equirectangular 4K @ 30fps (3840x2160)
        ↓
[Stella VSLAM: equirectangular camera model]
        ↓
SLAM Output (pose, map, trajectory)
```

**Stitching Options**:

| Method | Latency | Quality | Complexity |
|--------|---------|---------|------------|
| **Insta360 MediaSDK** (GPU) | <10ms | Excellent | Requires SDK approval (1 week) |
| OpenCV Stitcher | 50-200ms | Good | Open-source, slower |
| Pre-computed LUTs | 5-15ms | Good | Requires calibration |

**Configuration** (from GitHub Discussion #158):
```yaml
Camera:
  name: "Insta360 ONE X2"
  setup: "monocular"
  model: "equirectangular"
  fps: 30.0
  cols: 3840  # 4K recommended (not 5760)
  rows: 2160
  color_order: "RGB"

Feature:
  max_num_keypoints: 5000
  scale_factor: 1.2
  num_levels: 8
```

**Community Guidance**: "Lower resolution to 4K for better consistency" (from maintainer)

**Challenges**:
```
⚠️ Stitching adds latency (10-200ms depending on method)
⚠️ Requires Insta360 SDK (approval time: 3-7 days)
⚠️ Stitching artifacts at seams
⚠️ No intrinsic calibration for equirectangular (uses perfect projection assumption)
⚠️ Cannot use both fisheye independently (lose stereo depth info)
```

**Timeline**: 2-3 weeks (with SDK access)

---

**VERDICT**: **Tie (both have challenges)**
- VINS-Fusion: Need to extend for dual camera (research-proven)
- Stella VSLAM: Need to stitch first (Insta360-proven)

Both require additional work beyond out-of-box support.

---

### 3. Insta360 Compatibility

#### Stella VSLAM: Explicitly Supported ✅

**Evidence**:
```
✅ GitHub Discussion #158: "How to adjust parameters for Insta360 ONE X2?"
✅ Official README: "visual SLAM using equirectangular camera models
   (e.g. RICOH THETA series, **insta360 series**, etc)"
✅ stella_vslam_dense fork: "Tested with DJI Avata with Insta360 modules"
✅ Community success: ONE X2 @ 4K worked after parameter tuning
```

**Proven Configuration**:
- Camera: Insta360 ONE X2
- Resolution: 5760x2880 @ 30fps (original), **4K recommended**
- Outcome: Successful tracking after tuning
- Key finding: Lower to 4K for better consistency

**Setup with ai4ce/insta360_ros_driver**:
```bash
# ROS2 driver publishes:
/insta360/image_equirectangular    # Stitched 360° image
/insta360/image_raw/front          # Raw front fisheye
/insta360/image_raw/back           # Raw back fisheye

# Stella VSLAM subscribes to equirectangular
```

**Timeline**: 1-2 weeks (with SDK)

---

#### VINS-Fusion: Generic Fisheye Support ⚠️

**Evidence**:
```
✅ Native Kannala-Brandt fisheye model (industry standard)
✅ ~200° FOV supported (Insta360 ~195° fits)
⚠️ NO specific Insta360 examples found
⚠️ NO ready-made Insta360 driver for VINS
```

**Community Examples**:
- Intel RealSense T265 (dual fisheye ~163° FOV, overlapping stereo)
- MYNT EYE cameras
- Generic fisheye cameras

**Insta360 Integration Required**:

**Option A: Custom ROS Publisher**
```python
# Extract Insta360 frames via SDK, publish to ROS
import insta360_sdk
import rospy
from sensor_msgs.msg import Image

# Publish front fisheye to /camera/image_raw
# Publish IMU to /imu0
```

**Option B: Use ai4ce driver + topic remapping**
```bash
# ai4ce driver publishes separate front/back topics
# Remap to VINS expected topics
```

**Calibration**: Must calibrate yourself with Kalibr (Insta360 doesn't publish factory calibration)

**Timeline**: 2-3 days additional work for Insta360 interface

---

**VERDICT**: **Stella VSLAM wins** - Explicit Insta360 support with proven configs.

---

### 4. Performance Comparison

#### VINS-Fusion

**Published Benchmarks**:
- **FPS**: 20-30 Hz on i5-6600K (desktop)
- **Expected on GTX 5090**: 25-30+ FPS (CPU-based, will use multi-core well)
- **Accuracy**: 2-5 cm trajectory error (with IMU)
- **Drift**: <1% over 100m (with loop closure)
- **Initialization**: 2-3 seconds

**Resource Usage**:
- **CPU**: ~200% (2 cores), 25-30% on 8-core
- **GPU**: Minimal (VINS is CPU-based)
- **Memory**: 8-12 GB RAM

**Dual Camera Impact**:
```
Single fisheye: 25-30 FPS
Dual fisheye:   15-20 FPS (2x processing)
Still exceeds 1-30 FPS requirement ✅
```

---

#### Stella VSLAM

**Published Benchmarks**:
- **FPS**: 20-30 FPS for 4K equirectangular (desktop)
- **Estimated**: 22 FPS equivalent (0.045s median tracking)
- **Accuracy**: 2-3 cm (but accumulates drift without IMU)
- **Initialization**: ~2 seconds

**Resource Usage**:
- **CPU**: Similar to VINS
- **GPU**: Optional CUDA acceleration for feature extraction
- **Memory**: ~2-4GB for 4K

**With Stitching**:
```
Stitching (SDK): ~10ms
SLAM processing: ~45ms
Total latency:   ~55ms (18 FPS)
Still meets 1-30 FPS requirement ✅
```

---

**VERDICT**: **Tie** - Both meet 10-30 FPS target, similar accuracy in visual-only mode.

**But**: VINS-Fusion accuracy MUCH better with IMU (2-5 cm vs 5-10+ cm drift).

---

### 5. Drone Suitability ⭐ CRITICAL

#### VINS-Fusion: Purpose-Built for UAVs ✅✅

**Developed by**: HKUST Aerial Robotics Group (Prof. Shaojie Shen)
- **Specialization**: UAV state estimation
- **Real-world testing**: Extensive drone flight tests

**Proven Drone Projects**:
1. **Omni-swarm** (IEEE T-RO 2021)
   - Decentralized aerial swarm with dual fisheye VIO
   - Centimeter-level accuracy
   - Real autonomous drone flights

2. **DFOM** (J. Field Robotics 2020)
   - Real-time 3D mapping on drones
   - Dual fisheye omnidirectional
   - Onboard computation

3. **HKUST UAV Projects**
   - Indoor warehouse navigation
   - Forest flight tests
   - Aggressive maneuver handling

**Drone-Specific Features**:
```
✅ Optimized for fast motion (IMU handles aggressive maneuvers)
✅ Indoor-optimized (suited for indoor environments with rich features)
✅ Robust initialization (handles drone startup sequences)
✅ IMU-visual tight coupling (essential for safety)
✅ Loop closure (reduces long-term drift)
✅ Tested at 20-30 Hz camera, 200 Hz IMU (drone rates)
```

**Report Quote**:
> "Battle-tested for drones with proven track record"
> "Smallest RMSE on indoor drone tests"
> "Suited for indoor environments with rich visual features"

**Academic Impact**: 500+ citations, Best Paper Honorable Mention (IEEE T-RO 2018)

---

#### Stella VSLAM: General-Purpose Visual SLAM ⚠️

**Developed by**: stella-cv (general computer vision community)
- **Specialization**: 360° camera SLAM (RICOH THETA, Insta360)
- **Focus**: Visual-only SLAM for various camera types

**UAV Evidence**:
```
⚠️ stella_vslam_dense fork: "Real-time 3D reconstruction for 360° action cams on small UAVs"
   - Tested with: DJI Avata with Insta360 modules, GoPro Max
   - Performance: Real-time on HD equirectangular video
   - Publication: 2022 SSRR conference paper

✅ Shows UAV usage is possible
❌ But visual-only (no IMU) = risky for autonomous flight
```

**Limitations for Drones**:
```
❌ NO IMU integration (cannot handle IMU data)
❌ Visual-only = vulnerable to motion blur, fast rotation
❌ No scale from single camera (need external reference or stitching from dual)
❌ Tracking loss during aggressive maneuvers
❌ No fallback during visual occlusions
❌ Not designed for safety-critical applications
```

**Suitable for**:
- ✅ Action cameras on stabilized gimbals
- ✅ Handheld 360° mapping
- ✅ Slow-moving robots with good lighting
- ❌ **Autonomous drones** (needs IMU for safety)

---

**VERDICT**: **VINS-Fusion wins decisively**
- VINS-Fusion: Purpose-built for autonomous UAVs
- Stella VSLAM: General visual SLAM, risky without IMU

---

### 6. Setup Complexity

#### VINS-Fusion: Moderate-High Complexity

**Dependencies**:
```
- ROS Noetic (full ecosystem)
- Ceres Solver 1.14.0 (SPECIFIC VERSION required)
- Eigen3, OpenCV, catkin_tools
- Kalibr (for calibration)
- imu_utils (for IMU characterization)
```

**Installation Steps**:
1. Install ROS Noetic (30 min)
2. Build Ceres 1.14.0 from source (30 min)
3. Setup catkin workspace (10 min)
4. Clone and build VINS-Fusion (10-20 min)
5. Install Kalibr (30 min)
6. Create Insta360 interface (2-3 days)

**Calibration** (CRITICAL, time-consuming):
1. Camera intrinsic: 4-6 hours (per camera)
2. IMU noise characterization: 22 hours (mostly passive)
3. Camera-IMU extrinsic: 2-4 hours
4. Validation: 4-8 hours

**Total Setup Time**:
- Single camera: **2-3 weeks**
- Dual camera: **6-8 weeks**

**Difficulty**: ⭐⭐⭐⭐ (High)
- Requires ROS expertise
- Calibration is critical and complex
- IMU tuning requires understanding
- Debugging needs SLAM knowledge

---

#### Stella VSLAM: Lower Complexity

**Dependencies**:
```
- Docker OR manual build (Eigen, g2o, SuiteSparse, FBoW, yaml-cpp, OpenCV)
- Optional: CUDA for GPU acceleration
- Optional: Pangolin for visualization
```

**Installation Steps (Docker)**:
```bash
# 1. Docker build (recommended)
git clone --recursive https://github.com/stella-cv/stella_vslam.git
cd stella_vslam
docker build -t stella_vslam:cuda -f Dockerfile.cuda \
  --build-arg NUM_THREADS=$(nproc) .

# 2. Download vocabulary
wget https://github.com/stella-cv/FBoW_orb_vocab/raw/main/orb_vocab.fbow

# 3. Apply for Insta360 SDK (3-7 days approval)
# 4. Setup ai4ce/insta360_ros_driver
# 5. Configure equirectangular YAML
```

**Calibration**:
```
✅ Equirectangular needs NO intrinsic calibration (only cols, rows)
❌ BUT: Assumes perfect stitching from Insta360 SDK
⚠️ Still need to tune ORB feature parameters
```

**Total Setup Time**:
- With Docker + SDK: **1-2 weeks**
- Manual build: **2-3 weeks**

**Difficulty**: ⭐⭐ (Moderate)
- Docker simplifies dependencies
- No ROS required (can run standalone)
- Simpler parameter tuning
- Less calibration overhead

---

**VERDICT**: **Stella VSLAM wins** - Easier setup, Docker support, less calibration.

**But**: Simpler setup doesn't offset lack of IMU for drone applications.

---

### 7. Community & Documentation

#### VINS-Fusion

**Repository Health**:
- **Stars**: 4,200+
- **Forks**: 1,500+
- **License**: GPL-3.0
- **Last commit**: January 2019 (official frozen)
- **Community**: Very active (ROS2 ports, GPU extensions, Jetson, Docker)

**Documentation**:
- ✅ Comprehensive README
- ✅ Configuration examples (TUM-VI, EuRoC, RealSense)
- ✅ Calibration guides (Kalibr integration)
- ✅ Academic papers (IEEE T-RO 2018, 500+ citations)
- ✅ YouTube tutorials
- ✅ Active community forums

**Community Forks & Extensions**:
- VINS-Fisheye (GPU-accelerated for Jetson)
- VINS-OS (dual fisheye stereo for UAVs)
- ROS2 ports
- Docker containers

**Support**:
- Active GitHub Discussions
- ROS Discourse threads
- Extensive third-party tutorials

---

#### Stella VSLAM

**Repository Health**:
- **Stars**: 1,100+
- **Forks**: 443
- **License**: BSD-2-Clause (commercial-friendly)
- **Last commit**: 2024 (actively maintained)
- **Contributors**: 55

**Documentation**:
- ✅ Excellent README
- ✅ Official docs: https://stella-cv.readthedocs.io/
- ✅ Docker support (multiple Dockerfiles)
- ✅ ROS2 wrapper available
- ✅ GitHub Discussions active
- ⚠️ Fewer third-party tutorials than VINS

**Critical Resource**:
- **GitHub Discussion #158**: Insta360 ONE X2 parameters (MUST READ)
- Maintainer provides direct guidance on resolution/tuning

**Community**:
- Smaller than VINS-Fusion
- Active maintainers (responsive)
- Growing interest in 360° SLAM

---

**VERDICT**: **VINS-Fusion wins** - Larger community, more resources, extensive tutorials.

---

### 8. License Considerations

#### VINS-Fusion: GPL-3.0 ⚠️

**Implications**:
```
❌ Copyleft: Must open-source any modifications
❌ Restrictive for commercial closed-source products
⚠️ OK for research and open-source projects
```

**Commercial Alternative**: Contact HKUST for commercial license

---

#### Stella VSLAM: BSD-2-Clause ✅

**Implications**:
```
✅ Permissive: Can use in commercial products
✅ Can modify and keep proprietary
✅ No copyleft restrictions
✅ Commercial-friendly
```

---

**VERDICT**: **Stella VSLAM wins** - More permissive license.

**But**: For research/academic use, both are fine.

---

## Side-by-Side Feature Matrix

| Feature | VINS-Fusion | Stella VSLAM | Critical for Drones? |
|---------|-------------|--------------|---------------------|
| **IMU Integration** | ✅ Tightly-coupled | ❌ None | ⭐⭐⭐⭐⭐ YES |
| **Dual Non-Overlap Fisheye** | ⚠️ Extension (1-2wks) | ❌ Stitch required | ⭐⭐⭐ Medium |
| **Insta360 Support** | ⚠️ Generic fisheye | ✅ Explicit | ⭐⭐ Nice-to-have |
| **Performance (FPS)** | 25-30 | 20-30 | ⭐⭐⭐⭐ High |
| **Accuracy** | 2-5 cm (w/ IMU) | 2-3 cm (visual) | ⭐⭐⭐⭐ High |
| **Drift** | <1% (100m) | 2-5% (no IMU) | ⭐⭐⭐⭐⭐ YES |
| **Drone-Proven** | ✅✅ Purpose-built | ⚠️ Limited | ⭐⭐⭐⭐⭐ YES |
| **Setup Time** | 6-8 weeks | 1-2 weeks | ⭐⭐ Convenience |
| **Complexity** | ⭐⭐⭐⭐ High | ⭐⭐ Moderate | ⭐⭐ Convenience |
| **Community** | 4,200 stars | 1,100 stars | ⭐⭐⭐ Medium |
| **Documentation** | ✅ Excellent | ✅ Good | ⭐⭐ Convenience |
| **License** | GPL-3.0 | BSD-2 | ⭐ Low (research) |
| **ROS Dependency** | ✅ Required | ⚠️ Optional | ⭐⭐ Medium |
| **GPU Acceleration** | ⚠️ Community only | ✅ Native CUDA | ⭐⭐ Nice-to-have |
| **Loop Closure** | ✅ Yes | ✅ Yes | ⭐⭐⭐ Medium |

---

## Use Case Decision Matrix

### Choose VINS-Fusion If:

```
✅ You have an IMU and want to use it (drone scenario)
✅ Accuracy and robustness are critical (autonomous flight)
✅ You can invest 6-8 weeks in setup
✅ You're comfortable with ROS ecosystem
✅ You want proven drone performance
✅ Long-term drift reduction is important
✅ Safety is paramount (need IMU fallback)
```

**Confidence**: **85%** success (single fisheye), **70%** (dual fisheye)

---

### Choose Stella VSLAM If:

```
✅ You want fastest deployment (1-2 weeks)
✅ You can accept visual-only SLAM (risky for drones!)
✅ You want explicit Insta360 support
✅ You prefer Docker over ROS
✅ You need BSD license for commercial use
✅ You're doing mapping, not autonomous flight
✅ You can implement external IMU fusion yourself (1-2 weeks extra)
```

**Confidence**: **75%** success (but risky without IMU for drones)

---

### Hybrid Approach: Stella + External IMU Fusion

**What**: Use Stella VSLAM for vision, add loose IMU coupling externally

**How**:
```
Stella VSLAM (visual pose) → External EKF → Fused pose
         ↑                                  ↑
         └─────────── IMU data ────────────┘
```

**Effort**: +1-2 weeks to implement EKF fusion
**Accuracy**: Better than visual-only, worse than tightly-coupled
**Complexity**: Medium-High

**Tools**: robot_localization, GTSAM, custom EKF

**Recommendation**: **Only if you MUST have fast deployment AND explicit Insta360 support**. Still inferior to VINS-Fusion's tight coupling.

---

## Final Recommendation for Your Setup

### Your Requirements:
- ✅ Insta360 dual non-overlapping fisheye
- ✅ IMU available (6-axis @ 500Hz)
- ✅ Indoor drone
- ✅ 1-30 FPS minimum
- ✅ GTX 5090 available

### WINNER: **VINS-Fusion** 🏆

**Primary Reasons**:
1. **IMU integration is NON-NEGOTIABLE for autonomous drones**
   - Stella VSLAM has NO IMU support
   - Visual-only is unsafe for flight
   - VINS-Fusion's tight coupling provides 50-80% drift reduction

2. **Drone-proven heritage**
   - HKUST Aerial Robotics designed it FOR UAVs
   - Extensive real-world flight testing
   - Omni-swarm/DFOM prove dual fisheye works

3. **Better accuracy with IMU**
   - 2-5 cm with IMU vs 5-10+ cm drift without
   - <1% drift vs 2-5%+ drift

4. **Safety-critical**
   - IMU fallback during visual failures
   - Maintains state for emergency landing
   - Handles aggressive maneuvers

### Trade-offs You Accept:
- ⚠️ Higher setup complexity (6-8 weeks vs 1-2 weeks)
- ⚠️ Requires ROS ecosystem
- ⚠️ More difficult calibration
- ⚠️ Dual fisheye requires extension (research-proven)

### Why NOT Stella VSLAM:
```
❌ NO IMU integration (dealbreaker for drones)
❌ Visual-only = unsafe for autonomous flight
❌ Would need 1-2 weeks to add external IMU fusion (inferior to tight coupling)
❌ Stitching adds latency and artifacts
```

Even with Stella's advantages (explicit Insta360 support, easier setup), **the lack of IMU is a showstopper** for drone applications.

---

## Implementation Strategy

### Recommended Path: VINS-Fusion

**Phase 1 (Weeks 1-3): Single Fisheye + IMU**
```
→ Use front OR back fisheye
→ Validate VINS-Fusion works with Insta360
→ Calibrate with Kalibr
→ Test performance (expect 25-30 FPS)
→ DECISION: If single sufficient → DONE!
```

**Phase 2 (Weeks 4-6): Dual Fisheye Extension**
```
→ Approach A: Dual monocular (2 VINS instances)
→ Create fusion node
→ Test 360° coverage
→ Optimize performance
```

**Expected Results**:
- ✅ 15-25 FPS (dual camera)
- ✅ 2-5 cm accuracy
- ✅ <1% drift over 100m
- ✅ Robust to occlusion
- ✅ Safe for autonomous flight

---

### Alternative Path: Stella + IMU Fusion (NOT RECOMMENDED)

**Only consider if**:
- You MUST have <2 week deployment
- You MUST have explicit Insta360 support
- You're willing to implement external IMU fusion

**Timeline**: 3-4 weeks total (1-2 weeks Stella + 1-2 weeks IMU fusion)
**Accuracy**: Worse than VINS-Fusion tight coupling
**Risk**: Higher (custom IMU fusion code)

---

## Conclusion

For your **Insta360 dual fisheye + IMU drone** setup:

### ✅ CHOOSE: **VINS-Fusion**

**Why**: IMU integration is critical for drone safety and performance. VINS-Fusion provides tightly-coupled visual-inertial SLAM that Stella VSLAM cannot match.

**Timeline**: 6-8 weeks to dual fisheye system
**Confidence**: 85% (single), 70% (dual), 90% (with fallback)
**Safety**: Excellent (IMU fallback, emergency landing capability)

### ❌ AVOID: **Stella VSLAM** (for autonomous drones)

**Why**: Visual-only SLAM without IMU is unsafe for autonomous flight, despite easier setup and explicit Insta360 support.

**Acceptable for**: Mapping missions with manual flight, action camera recording
**Not acceptable for**: Autonomous navigation, safety-critical applications

---

**The decision is clear: VINS-Fusion is the right choice for your drone project.**

Follow the implementation plan in `VINS_FUSION_DUAL_FISHEYE_IMPLEMENTATION_PLAN.md` to get started.

---

**Questions? See**:
- `RECOMMENDATION_SUMMARY.md` - Full analysis of all 7 methods
- `VINS_FUSION_DUAL_FISHEYE_IMPLEMENTATION_PLAN.md` - Step-by-step guide
- Research reports in `shorter research reports/` folder
