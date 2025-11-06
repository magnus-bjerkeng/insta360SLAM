# SLAM Research Plan: Insta360 Dual Fisheye + IMU for Indoor Drone

## Setup Specifications
- **Camera**: Insta360 with two non-overlapping fisheye lenses
- **Sensors**: IMU available, extrinsic calibration known
- **Environment**: Indoor drone navigation
- **Hardware**: GTX 5090, Ubuntu
- **Performance**: Minimum 1fps required
- **Language**: Any performant language

---

## Research Phase 1: ORB-SLAM3 Analysis

### 1.1 Core Compatibility Assessment
**Questions to investigate:**
- Does ORB-SLAM3 support dual fisheye cameras natively?
- Can ORB-SLAM3 handle non-overlapping camera configurations?
- What fisheye camera models does ORB-SLAM3 support? (Kannala-Brandt, equidistant, etc.)
- Does it support multi-camera systems where cameras don't share a field of view?

**Key Resources:**
- ORB-SLAM3 paper (2020) - multi-session and inertial integration
- GitHub: https://github.com/UZ-SLAMLab/ORB_SLAM3
- Documentation on camera models and multi-camera support

### 1.2 IMU Integration Capabilities
- ORB-SLAM3 VI (Visual-Inertial) mode compatibility
- IMU initialization requirements
- Expected performance with fisheye + IMU

### 1.3 Expected Limitations
**Likely challenges:**
- **Non-overlapping cameras**: ORB-SLAM3 typically assumes stereo overlap or monocular. Two separate fisheye views may require custom modifications
- **Feature tracking**: Non-overlapping views mean no stereo matching between cameras
- **Coordinate system**: Need to carefully handle the dual camera coordinate frames
- **Real-time performance**: Fisheye undistortion and feature extraction overhead

### 1.4 Modification Requirements
If ORB-SLAM3 needs adaptation:
- Custom camera model implementation
- Multi-camera tracking logic (independent tracking per camera)
- Map merging from two separate views
- Modified loop closure detection

### 1.5 Expected Performance
- Baseline FPS expectations with GTX 5090
- Scalability with fisheye resolution

---

## Research Phase 2: Alternative SLAM Systems

### 2.1 Fisheye-Specific SLAM Systems

#### A. Basalt (Visual-Inertial Odometry)
- **GitHub**: https://github.com/VladyslavUsenko/basalt-headers
- **Strengths**:
  - Excellent fisheye support (multiple camera models)
  - Multi-camera VIO with IMU
  - High performance, GPU-accelerated
  - Non-overlapping cameras explicitly supported
- **Language**: C++
- **Investigation priorities**:
  - Multi-camera fisheye configuration
  - Real-time performance benchmarks
  - Indoor performance characteristics

#### B. VINS-Fusion / VINS-Mono
- **GitHub**:
  - VINS-Fusion: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion
  - VINS-Mono: https://github.com/HKUST-Aerial-Robotics/VINS-Mono
- **Strengths**:
  - Proven drone SLAM performance
  - IMU-visual fusion
  - Fisheye support in VINS-Fusion
  - Multi-camera support
- **Language**: C++, ROS
- **Investigation priorities**:
  - Dual non-overlapping fisheye configuration
  - Indoor performance
  - GPU utilization potential

#### C. Kimera-VIO
- **GitHub**: https://github.com/MIT-SPARK/Kimera-VIO
- **Strengths**:
  - Real-time VIO with semantic understanding
  - Multi-camera support
  - IMU integration
  - Modern architecture
- **Language**: C++, ROS
- **Investigation priorities**:
  - Fisheye camera model support
  - Performance on GTX 5090

#### D. OpenVINS
- **GitHub**: https://github.com/rpng/open_vins
- **Strengths**:
  - Multi-camera VIO
  - Strong fisheye support (Kannala-Brandt model)
  - Filter-based approach (EKF) - potentially faster
  - Excellent documentation
- **Language**: C++, ROS
- **Investigation priorities**:
  - Dual fisheye configuration examples
  - Performance metrics

### 2.2 Omnidirectional/360° SLAM Systems

#### A. MapLab / ROVIO
- **GitHub**:
  - MapLab: https://github.com/ethz-asl/maplab
  - ROVIO: https://github.com/ethz-asl/rovio
- **Strengths**:
  - Multi-camera multi-IMU framework
  - Designed for complex sensor configurations
  - Used in research drones
- **Investigation priorities**:
  - Fisheye support
  - Setup complexity

#### B. Multi-Camera SLAM Research Projects
Search for:
- "Multi-camera SLAM fisheye"
- "360 camera SLAM"
- "Omnidirectional SLAM"
- "Non-overlapping camera SLAM"

### 2.3 Learning-Based Approaches

#### A. DROID-SLAM
- **GitHub**: https://github.com/princeton-vl/DROID-SLAM
- **Strengths**:
  - Deep learning-based
  - GPU-accelerated (excellent for RTX 5090)
  - Can generalize to different camera models
- **Weaknesses**:
  - May need training/fine-tuning for fisheye
  - IMU integration unclear
- **Investigation priorities**:
  - Fisheye compatibility
  - Training requirements

#### B. TartanVO / TartanCalib
- **GitHub**:
  - TartanVO: https://github.com/castacks/tartanvo
  - TartanCalib: https://github.com/castacks/TartanCalib
- **Strengths**:
  - Learning-based VO
  - Robust to challenging conditions
- **Investigation priorities**:
  - Fisheye support
  - Real-time capability

---

## Research Phase 3: Specialized Drone SLAM

### 3.1 Drone-Specific Systems

#### A. Fast-LIO / Fast-LIO2
- **GitHub**: https://github.com/hku-mars/FAST_LIO
- **Note**: Primarily LiDAR-based, but worth investigating if they have camera variants
- Extremely fast IMU integration

#### B. SVO (Semi-Direct Visual Odometry)
- **GitHub**: https://github.com/uzh-rpg/rpg_svo_pro
- **Strengths**:
  - Designed for drones
  - Multi-camera support
  - Fast performance
- **Investigation priorities**:
  - Fisheye support
  - Dual camera setup

### 3.2 Research Databases to Search
- **arXiv**: Papers on "fisheye SLAM", "dual fisheye odometry", "omnidirectional SLAM"
- **Google Scholar**: Same search terms, look for code repositories
- **IEEE Xplore**: Recent conference papers (ICRA, IROS, CVPR) with code
- **Papers with Code**: Search "Visual SLAM fisheye"

---

## Research Phase 4: Insta360-Specific Solutions

### 4.1 Insta360 SDK Investigation
- Check if Insta360 provides SDK for camera access
- Pre-processing tools for dual fisheye frames
- Calibration data format

### 4.2 Community Projects
Search GitHub/GitLab for:
- "Insta360 SLAM"
- "Insta360 VIO"
- "Insta360 odometry"
- Any research using Insta360 for robotics

### 4.3 Existing Dual Fisheye Research
- Samsung Gear 360
- Ricoh Theta
- Other dual fisheye systems used in SLAM research

---

## Research Phase 5: Performance Benchmarking Strategy

### 5.1 Test Datasets
Identify or create datasets with:
- Dual fisheye camera feeds
- Synchronized IMU data
- Indoor environments
- Ground truth if possible

**Public datasets to investigate:**
- EuRoC MAV dataset (stereo + IMU, good baseline)
- TUM VI dataset (fisheye + IMU)
- UZH-FPV dataset (drone-specific)

### 5.2 Performance Metrics
- **Trajectory accuracy**: ATE, RPE
- **Frame rate**: Target >1fps, measure actual
- **CPU/GPU utilization**
- **Memory footprint**
- **Initialization time**
- **Loop closure performance**

### 5.3 Benchmark Suite
Create standardized tests:
1. Static initialization
2. Slow translation
3. Fast aggressive flight
4. Rotation-heavy sequences
5. Texture-poor environments
6. Loop closures

---

## Research Phase 6: Implementation Path Decision

### 6.1 Selection Criteria Ranking
After research, evaluate candidates on:

**Must-have (eliminate if not met):**
- [ ] Runs on Ubuntu with GTX 5090
- [ ] Achieves >1fps with dual fisheye
- [ ] Supports IMU integration
- [ ] Handles fisheye distortion models

**High priority:**
- [ ] Native support for non-overlapping cameras
- [ ] Active development/maintenance
- [ ] Good documentation
- [ ] Proven indoor performance
- [ ] Minimal modification required

**Nice to have:**
- [ ] ROS/ROS2 integration
- [ ] Loop closure detection
- [ ] Map saving/loading
- [ ] Visualization tools

### 6.2 Top 3 Candidates Shortlist
After initial research, identify:
1. **Best out-of-box**: System requiring least modification
2. **Best performance**: Highest FPS and accuracy
3. **Best long-term**: Most maintainable and extensible

---

## Research Phase 7: Prototype & Validation

### 7.1 Quick Validation Tests
For each shortlisted candidate:
1. Build and compile (document any issues)
2. Run with sample data (webcam or dataset)
3. Measure baseline performance
4. Test fisheye camera model support

### 7.2 Integration Planning
For top candidate(s):
- Camera driver integration
- IMU data pipeline
- Calibration file format conversion
- Real-time optimization tuning

### 7.3 Fallback Strategy
If no system works out-of-box:
- **Option A**: Modify ORB-SLAM3 for dual fisheye
- **Option B**: Use single fisheye + IMU (sacrifice one camera)
- **Option C**: Custom implementation using:
  - OpenCV fisheye undistortion
  - Feature tracking (ORB, SIFT, learned features)
  - IMU fusion library (e.g., GTSAM, Ceres)
  - Reference architecture from open source project

---

## Timeline Estimate

- **Phase 1 (ORB-SLAM3)**: 2-3 days
- **Phase 2 (Alternatives)**: 3-5 days
- **Phase 3 (Drone-specific)**: 2 days
- **Phase 4 (Insta360-specific)**: 1-2 days
- **Phase 5 (Benchmarking prep)**: 1 day
- **Phase 6 (Decision)**: 1 day
- **Phase 7 (Prototype)**: 3-5 days

**Total**: ~2-3 weeks for thorough research and initial prototyping

---

## Next Steps

1. **Start with Phase 1**: Deep dive into ORB-SLAM3 capabilities
2. **Document findings**: Create detailed notes for each system investigated
3. **Build comparison matrix**: Features, performance, ease of integration
4. **Make go/no-go decision**: Select system or plan custom development
5. **Begin integration**: Start with most promising candidate

---

## Key Questions to Answer

- [ ] Can we use cameras independently (two monocular SLAM) and merge maps?
- [ ] Is it better to treat as a single "wide FOV" system or two separate cameras?
- [ ] What's the trade-off between accuracy and performance?
- [ ] Do we need loop closure for indoor drone navigation?
- [ ] What's the minimum feature richness required in indoor environments?
- [ ] Should we consider alternative sensors (depth cameras, LiDAR)?

---

## Notes & References

### Fisheye Camera Models
- **Kannala-Brandt**: Most common for wide FOV fisheye
- **Equidistant**: Alternative model
- **Unified Camera Model**: Generic approach
- **OpenCV fisheye model**: Widely supported

### Sensor Fusion
- IMU pre-integration (Forster et al.)
- Loosely vs. tightly coupled fusion
- Optimization frameworks: GTSAM, Ceres Solver, g2o

### Challenges Unique to This Setup
1. **Non-overlapping fields of view**: Can't do traditional stereo
2. **Independent tracking**: Each camera tracks separately until features can be related through motion
3. **Map alignment**: Need to properly merge/align maps from two views
4. **Degenerate motion**: Fisheye cameras can struggle with pure rotation
5. **IMU critical**: Will be primary constraint for relating two camera views

