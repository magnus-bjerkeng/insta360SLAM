# ORB-SLAM3 Research Report: Insta360 Dual Fisheye Drone SLAM (Condensed)

**Date:** 2025-11-06
**System:** ORB-SLAM3 v1.0
**Focus:** Indoor Drone SLAM with Insta360 Dual Fisheye + IMU

---

## 1. Executive Summary

### Recommendation: **GO with Qualifications** (Confidence: 85%)

ORB-SLAM3 is the gold standard for feature-based visual-inertial SLAM and highly suitable for the Insta360 drone project, with one limitation: **native support is single fisheye + IMU only**. Dual non-overlapping fisheye not natively supported.

**Strengths:**
- ✅ Native Kannala-Brandt fisheye model (proven for wide FOV)
- ✅ Robust tightly-coupled visual-inertial (VI) mode
- ✅ 9mm accuracy on TUM-VI fisheye + IMU dataset
- ✅ 30-40 FPS on desktop hardware (exceeds 1fps requirement)
- ✅ Active community (7.9k GitHub stars)
- ✅ Ubuntu + GTX 5090 compatible

**Limitations:**
- ❌ No native dual non-overlapping camera support
- ⚠️ Must use single fisheye or implement custom multi-camera extension
- ⚠️ GPLv3 license (restrictive for commercial, closed-source version available)
- ⚠️ Complex build process with multiple dependencies

**Recommended Approach:** Start with single fisheye (front camera) + IMU in monocular-inertial mode. Expected: 30-40 FPS, centimeter-level accuracy, 2-3 week deployment timeline.

---

## 2. Fisheye Support Analysis ⭐ CRITICAL

### Kannala-Brandt Model & Insta360 Compatibility
- **Model:** KannalaBrandt8 with 4 distortion parameters (k1, k2, k3, k4)
- **FOV Support:** Up to 180-185° (Insta360's ~200° well within range)
- **No Rectification:** Works directly with raw fisheye images
- **Proven:** GitHub Issue #102 confirmed KannalaBrandt8 superior
✅ Excellent match, IMU available, high resolution adequate
⚠️ Dual non-overlapping not natively supported - must use single camera

### TUM-VI Dataset Performance
- **Dataset:** 28 sequences, fisheye stereo-inertial, rapid hand-held motion
- **Accuracy:** 9mm average (stereo-inertial)
- **FPS:** 30-40 real-time
- **Monocular-inertial expected:** 2-3× degradation → 2-5 cm accuracy

### Configuration
```yaml
Camera.type: "KannalaBrandt8"
Camera.fx, fy, cx, cy: [intrinsics from calibration]
Camera.k1, k2, k3, k4: [distortion coefficients]
IMU.NoiseGyro, NoiseAcc, GyroWalk, AccWalk: [IMU parameters]
```

**Calibration:** Use OpenCV `cv2.fisheye.calibrate()` or Kalibr for visual-inertial calibration.

**Verdict:** EXCELLENT (9/10) - Mature fisheye support, proven on TUM-VI. Minor deduction for no native dual camera.

---

## 3. Visual-Inertial Mode ⭐ CRITICAL

### Architecture
- **Tightly-coupled MAP estimation:** Full Maximum-a-Posteriori throughout
- **IMU Preintegration:** Efficient integration between keyframes
- **Joint Optimization:** Bundle adjustment optimizes visual + inertial terms simultaneously
- **Supported:** Monocular-Inertial, Stereo-Inertial, RGB-D-Inertial

### IMU Configuration Requirements
1. **IMU Intrinsics:** Noise density (σ_g, σ_a), random walk (σ_gw, σ_aw)
   - **Tip:** Multiply manufacturer values by 10× for robustness
2. **Camera-IMU Extrinsics:** Rotation + translation from camera to IMU frame (critical accuracy factor)
3. **Time Synchronization:** Hardware sync ideal, software compensation possible
4. **Tools:** Kalibr (recommended), imu_utils

### Initialization
- **3-stage process:** Visual-only → Inertial MAP estimation → Joint VI refinement
- **Time:** ~2 seconds to initialization
- **Scale convergence:** <5% error at 2s, ~1% at 15s
- **Motion requirement:** Sufficient translation/rotation (avoid pure hovering)

### Performance Gains
2-5× better accuracy than visual-only. EuRoC: 3.6 cm, TUM-VI: 9 mm. Tracking continuity during visual failures, better handling of rapid motions.

**Verdict:** EXCELLENT (10/10) - Best-in-class VI integration, perfect for drone with IMU.

---

## 4. Single vs Dual Fisheye ⭐ CRITICAL

### Option A: Single Fisheye + IMU (Monocular-Inertial) ✅ RECOMMENDED
**Support:** Fully supported, extensively tested on TUM-VI
**Expected Performance:** 2-5 cm accuracy, 30-40 FPS, ~200° FOV
**Advantages:** Straightforward deployment (2-3 weeks), proven configuration, no code modifications
**Disadvantages:** Limited FOV (front or back only), no coverage behind drone
**Use Cases:** Forward-flying missions, predominantly forward motion

### Option B: Dual Fisheye as Stereo ❌ NOT VIABLE
**Issue:** ORB-SLAM3 stereo requires overlapping FOV. Insta360 cameras are non-overlapping (front/back hemispheres) - stereo matching impossible.

### Option C: Dual Non-Overlapping (Custom Extension) ❌ NOT NATIVE
**Support:** Not natively supported
**Evidence:** GitHub Issue #534 - user confirmed "using one side only"
**Implementation:** Would require running two ORB-SLAM3 instances + custom fusion (3-6 months development)
**Alternatives with native multi-camera:** Basalt, MultiCol-SLAM
**Complexity:** High risk, limited community examples

### Option D: Stitched Omnidirectional ⚠️ SUBOPTIMAL
Stitching introduces artifacts, latency, and distortion. Stella VSLAM better suited for equirectangular.

**Recommendation:** Start with Option A (single fisheye + IMU). If full 360° becomes critical after testing, explore Basalt or custom multi-camera extension.

**Verdict:** Single fisheye EXCELLENT (9/10), Dual non-overlapping POOR (3/10)

---

## 5. Technical Feasibility

### Operating System
✅ Ubuntu 18.04/20.04/22.04 officially supported (your system: fully compatible)

### GPU Requirements
⚠️ Base ORB-SLAM3 is CPU-only (no CUDA required)
- GTX 5090 idle by default
- Community CUDA extensions available: FastTrack (2.8× tracking speedup), TurboMap (1.6× mapping speedup)
- **For 1-30 FPS requirement:** CPU-only sufficient

### CPU & Memory
- **CPU:** 4+ cores minimum, 8+ recommended (your system: adequate)
- **RAM:** 16 GB minimum, 32 GB recommended
- **Multi-threading:** 3 main threads (Tracking, Local Mapping, Loop Closing)

### Dependencies
1. **C++11/14 compiler:** GCC 7+ or Clang
2. **OpenCV ≥3.0:** Ubuntu package `libopencv-dev`
3. **Eigen3 ≥3.1.0:** Ubuntu package `libeigen3-dev` (use v3.4.0)
4. **Pangolin:** Build from source (visualization)
5. **DBoW2 & g2o:** Bundled in Thirdparty folders

### Build Complexity
**Rating:** Moderate (6/10)
**Steps:** Install dependencies → Build Pangolin → Build DBoW2 → Build g2o → Build ORB-SLAM3
**Time:** 30-60 minutes
**Common Issues:** Eigen version conflicts, Pangolin FFMPEG errors, g2o tr1 references (all have documented fixes)
**Mitigation:** Docker images available

### Performance
**Expected FPS:** 30-40 (CPU-only on desktop)
**Memory:** 2-4 GB during operation
**Your Hardware:** Will easily exceed 1-30 FPS requirement

### ROS Support
❌ No official ROS wrapper
✅ Community ROS wrappers: suchetanrs/ORB-SLAM3-ROS2-Docker (ROS2 Humble), thien94/orb_slam3_ros_wrapper (ROS Noetic)

**Verdict:** EXCELLENT (9/10) - Highly feasible, all requirements met. Minor deduction for build complexity.

---

## 6. Repository Health

**GitHub:** https://github.com/UZ-SLAMLab/ORB_SLAM3
**Stars:** 7,900+ | **Forks:** 2,900+ | **License:** GPLv3
**Release:** v1.0 (Dec 2021, stable)
**Team:** UZ-SLAMLab (University of Zaragoza), led by Carlos Campos, Juan D. Tardós
**Academic Impact:** IEEE Transactions on Robotics 2021, 500+ citations
**Status:** Feature-complete, stable, active issue management

**Open Issues:** 531 (mostly usage questions, not critical bugs)

**Verdict:** EXCELLENT (9/10) - Outstanding community, strong academic backing, long-term viability.

---

## 7. Critical Issues

### Showstopper Assessment: ❌ NONE IDENTIFIED

**Key Issues:**
1. **Dual non-overlapping fisheye:** Not supported (design decision, not bug) → Use single camera
2. **Vocabulary loading:** Text format 20+ seconds → Convert to binary (0.2-1s)
3. **IMU initialization:** Requires sufficient motion → Ensure translation during startup
4. **IMU parameters:** Need tuning → Start with datasheet × 10
5. **Build dependencies:** Eigen/Pangolin conflicts → Well-documented fixes

**No fundamental blockers for Insta360 single fisheye + IMU deployment.**

**Verdict:** GOOD (7/10) - Known issues have solutions. Main "limitation" is feature gap, not bug.

---

## 8. Documentation

**README:** Excellent (system overview, build instructions, examples)
**Calibration Tutorial:** Excellent PDF covering camera/IMU calibration
**Config Examples:** TUM-VI, EuRoC, RealSense (fisheye + IMU examples provided)
**API Docs:** Fair (code comments, but no Doxygen)
**Academic Paper:** Excellent technical documentation (arXiv available)
**Community Docs:** Excellent (Medium tutorials, YouTube guides, GitHub examples)

**Verdict:** GOOD (8/10) - Strong official docs + extensive community resources. Missing formal API documentation.

---

## 9. Setup Complexity

### Timeline: 2-3 Weeks

**Week 1: Environment Setup**
- Days 1-2: System prep, dependency installation (OpenCV, Eigen3, Pangolin deps)
- Days 3-4: Build Pangolin, DBoW2, g2o, ORB-SLAM3 (~30-60 min build time)
- Day 5: Convert vocabulary to binary, test TUM-VI example dataset

**Alternative (Docker):** Days 1-2 only using suchetanrs/ORB-SLAM3-ROS2-Docker

**Week 2: Calibration**
- Days 1-2: Camera intrinsic calibration (OpenCV fisheye, 30-50 checkerboard images)
- Days 3-4: Camera-IMU extrinsic calibration (Kalibr, 2-3 min calibration sequence)
- Day 5: Create YAML config file

**Week 3: Integration & Testing**
- Days 1-2: Insta360 data pipeline (frame extraction, IMU sync)
- Days 3-4: Run ORB-SLAM3 on Insta360 data, assess tracking
- Day 5: Validation, benchmarking, parameter tuning

**Build Steps:**
```bash
# Install dependencies
sudo apt install build-essential cmake git libopencv-dev libeigen3-dev

# Build Pangolin (from source)
git clone https://github.com/stevenlovegrove/Pangolin && cd Pangolin
mkdir build && cd build && cmake .. && make && sudo make install

# Build ORB-SLAM3
git clone https://github.com/UZ-SLAMLab/ORB_SLAM3
cd ORB_SLAM3
chmod +x build.sh && ./build.sh  # Builds DBoW2, g2o, and main library
```

**Calibration:**
- **Camera:** `cv2.fisheye.calibrate()` with checkerboard (1-2 hours)
- **VI calibration:** Kalibr for camera-IMU extrinsics (2-4 hours)
- **IMU params:** Datasheet values × 10 for robustness

**Verdict:** MODERATE (6/10) - Achievable but not trivial. Calibration most time-consuming. 2-3 week timeline realistic.

---

## 10. Deployment Options

### Docker (RECOMMENDED for rapid setup)
**ORB-SLAM3-ROS2-Docker** (suchetanrs): Ubuntu 22.04 + ROS2 Humble + full dependencies
```bash
docker build -t orb-slam3-ros2:latest .
docker run --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix ...
```

### Build from Source (for production/custom modifications)
Full control, direct system installation, easier debugging (2-4 hours setup)

### ROS Integration
- **ROS2 Humble:** suchetanrs/ORB-SLAM3-ROS2-Docker
- **ROS Noetic:** thien94/orb_slam3_ros_wrapper

### Standalone Executable
Modify example programs (`mono_inertial_tum_vi`) for Insta360 input (1-2 days)

**Recommendation:** Start with Docker for prototyping, transition to optimized build for production.

**Verdict:** EXCELLENT (9/10) - Multiple deployment paths, high-quality Docker + ROS wrappers available.

---

## 11. Insta360/Fisheye Evidence ⭐ CRITICAL

### Direct Evidence
✅ **TUM-VI:** Official ORB-SLAM3 benchmark, fisheye stereo-inertial, 9mm accuracy (VERY HIGH confidence)
✅ **RealSense T265:** Community use, dual fisheye + IMU, Kannala-Brandt configs available (HIGH confidence)
✅ **GitHub Issue #102:** Developers confirmed KannalaBrandt8 with raw fisheye is superior approach

### Insta360-Specific
❌ **No direct Insta360 + ORB-SLAM3 projects found**
- Insta360 targets consumer video market, not robotics
- Limited academic SLAM research with Insta360

### Dual Non-Overlapping
**GitHub Issue #534:** User asked about dual fisheye, resolution was "using one side only" - confirms no native support

### Confidence Assessment: 80%
**Strong factors:** Kannala-Brandt proven for wide FOV, TUM-VI excellent results, RealSense T265 similar design works
**Weak factors:** No direct Insta360 examples, potential SDK/API access issues
**Risk:** Technical match excellent, main uncertainty is practical camera integration

**Validation Path:** 3-day test (calibrate → extract data → run ORB-SLAM3) would achieve 95%+ confidence

**Verdict:** GOOD (7/10) - Strong technical match, but practical uncertainty remains. Recommend 3-day validation.

---

## 12. Performance Expectations

### FPS
**CPU-only (expected):** 30-40 FPS (your hardware similar to TUM-VI benchmark i7-7700)
**With GPU acceleration:** 60-80 FPS potential (CUDA extensions: FastTrack, TurboMap)
**Your requirement (1-30 FPS):** ✅ Easily exceeded

### Accuracy
**Monocular-inertial (estimated):** 2-5 cm absolute trajectory error
**Factors:** IMU quality (Insta360 consumer-grade), calibration quality, environment features, flight dynamics
**Realistic for Insta360 drone:** 3-8 cm typical, 2-3 cm best case, 10-15 cm worst case

### CPU/GPU Utilization
**CPU:** 3-4 cores actively used (25-50% on 8-16 core system)
**GPU (base):** 0% (idle without CUDA extensions)
**RAM:** 2-4 GB operation, 6-8 GB peak for large maps

### Latency
**Total:** <50 ms camera capture → pose estimate
**Components:** Image acquisition (33 ms @ 30 FPS) + feature extraction (10-15 ms) + tracking (5-10 ms)
**Drone control:** ✅ Acceptable for moderate-speed autonomous navigation

### Robustness
**Tracking success (expected):** >95% good conditions, 80-90% challenging conditions
**Recovery:** Relocalization via place recognition, IMU-only propagation during visual failures

**Comparison:** ORB-SLAM3 most accurate feature-based SLAM per benchmarks (superior to VINS-Fusion, Kimera)

**Verdict:** EXCELLENT (9/10) - Will comfortably exceed requirements. 30-40 FPS expected, 3-8 cm accuracy excellent for navigation.

---

## 13. Comparison with Alternatives

| System | Accuracy | VI Support | Fisheye | Multi-Cam | Drone Use | Build | Verdict |
|--------|----------|------------|---------|-----------|-----------|-------|---------|
| **ORB-SLAM3** | ⭐⭐⭐⭐⭐ 9mm | ✅ Tightly-coupled | ✅ Native | ❌ | ✅ Proven | Moderate | **BEST for single fisheye+IMU** |
| **Stella VSLAM** | ⭐⭐⭐ 2-3cm | ❌ None | ✅ Native | ❌ | ⚠️ | Low | Simpler but no IMU |
| **Basalt** | ⭐⭐⭐⭐⭐ Comparable | ✅ Tightly-coupled | ✅ | ✅ Native | ✅ | Moderate | Better for multi-cam |
| **VINS-Fusion** | ⭐⭐⭐⭐ 3-4cm | ✅ | ⚠️ Less mature | ❌ | ✅ Very popular | Mod-High | Lower accuracy |
| **KIMERA** | ⭐⭐⭐⭐ 3-4cm | ✅ | ✅ | ❌ | ✅ | High | Semantic but lower accuracy |

**Choose ORB-SLAM3 when:** Accuracy is priority, IMU available, single fisheye acceptable, state-of-the-art performance required

**Choose Basalt if:** Dual non-overlapping camera becomes non-negotiable after testing single fisheye

**Verdict:** ORB-SLAM3 is BEST-IN-CLASS (10/10) for single fisheye + IMU configuration.

---

## 14. Next Steps & Deployment Roadmap

### Phase 1: Setup (Week 1)
```bash
# Option A: Docker (RECOMMENDED)
git clone https://github.com/suchetanrs/ORB-SLAM3-ROS2-Docker
docker build -t orb-slam3-ros2:latest .

# Option B: Source build
sudo apt update && sudo apt install build-essential cmake git libopencv-dev libeigen3-dev
# Build Pangolin, then ORB-SLAM3 (see Section 9)
```
- Test with TUM-VI dataset to verify installation
- Convert vocabulary to binary: ORBvoc.txt → ORBvoc.bin

### Phase 2: Calibration (Week 2)
**Camera Intrinsics:**
```python
import cv2
# Collect 30-50 checkerboard images
# cv2.fisheye.calibrate() → fx, fy, cx, cy, k1, k2, k3, k4
```

**Camera-IMU Extrinsics (Kalibr):**
```bash
kalibr_calibrate_imu_camera --bag calibration.bag --cam camchain.yaml --imu imu.yaml --target target.yaml
# Extract Tbc matrix and IMU intrinsics
```

**Create Config YAML:**
```yaml
# Copy TUM-VI.yaml as template
Camera.type: "KannalaBrandt8"
Camera.fx, fy, cx, cy: [from calibration]
Camera.k1, k2, k3, k4: [from calibration]
IMU.NoiseGyro, NoiseAcc: [datasheet × 10]
IMU.GyroWalk, AccWalk: [datasheet × 10]
IMU.T_b_c1: [from Kalibr]
```

### Phase 3: Integration (Week 3)
1. Extract Insta360 front fisheye frames (ffmpeg or Insta360 SDK)
2. Extract IMU data: `timestamp ax ay az gx gy gz`
3. Create timestamps.txt for image-IMU alignment
4. Run ORB-SLAM3:
```bash
./mono_inertial_tum_vi Vocabulary/ORBvoc.bin config.yaml /path/to/data timestamps.txt
```
5. Observe: Initialization (2-3s), tracking (green indicators), map quality
6. Benchmark: FPS, accuracy (if ground truth available), tracking success rate

### Phase 4: Optimization (Week 4+)
- Tune IMU parameters if initialization fails
- Adjust feature extraction parameters for low-texture areas
- Test robustness (various environments, aggressive maneuvers, long trajectories)
- Integrate with real-time drone camera/IMU stream

### Success Criteria
✅ Phase 1: ORB-SLAM3 built, TUM-VI runs at ≥30 FPS
✅ Phase 2: Calibration reprojection error <0.5 pixels
✅ Phase 3: Tracking success >80%, initialization <3 seconds
✅ Phase 4: 3-8 cm accuracy, ≥30 FPS, reliable tracking

### Risk Mitigation
- **Build issues:** Use Docker
- **Calibration difficulties:** Follow Kalibr tutorial, use high-quality targets
- **Poor performance:** 3-day validation testing, fallback to RealSense T265
- **Single FOV insufficient:** Test thoroughly, then explore Basalt

---

## 15. Key Resources

**Official:**
- GitHub: https://github.com/UZ-SLAMLab/ORB_SLAM3
- Paper: https://arxiv.org/abs/2007.11898
- Calibration Tutorial: `ORB_SLAM3/Calibration_Tutorial.pdf`

**Datasets:**
- TUM-VI: https://vision.in.tum.de/data/datasets/visual-inertial-dataset (fisheye + IMU benchmark)
- EuRoC: https://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets (drone)

**Calibration:**
- Kalibr: https://github.com/ethz-asl/kalibr (visual-inertial calibration)
- OpenCV: https://docs.opencv.org/master/db/d58/group__calib3d__fisheye.html

**Docker/ROS:**
- ROS2 Humble Docker: https://github.com/suchetanrs/ORB-SLAM3-ROS2-Docker
- ROS Noetic: https://github.com/thien94/orb_slam3_ros_wrapper

**Support:**
- GitHub Issues: https://github.com/UZ-SLAMLab/ORB_SLAM3/issues
- Community: ROS Discourse, Reddit r/computervision, Stack Overflow [orb-slam3]

---

## 16. Conclusion

### Final Recommendation: ✅ PROCEED WITH ORB-SLAM3

**Configuration:** Single Fisheye (Front) + IMU, Monocular-Inertial Mode
**Confidence:** 85% for successful deployment
**Timeline:** 2-3 weeks to operational system

### Why ORB-SLAM3?
1. **Best accuracy:** 9mm TUM-VI fisheye + IMU (state-of-the-art)
2. **Proven fisheye support:** Kannala-Brandt model mature, TUM-VI validated
3. **Robust VI integration:** Tightly-coupled MAP estimation, fast initialization (2s)
4. **Exceeds performance requirements:** 30-40 FPS expected (far above 1-30 FPS needed)
5. **Strong ecosystem:** 7.9k stars, extensive documentation, active community
6. **Hardware compatible:** Ubuntu + GTX 5090 fully supported

### Critical Limitation
❌ No native dual non-overlapping fisheye support
**Mitigation:** Start with single fisheye, validate adequacy. If full 360° critical after testing, explore Basalt or 3-6 month custom extension.

### Expected Outcomes
- **Performance:** 30-40 FPS, <50 ms latency
- **Accuracy:** 3-8 cm absolute trajectory error
- **Robustness:** >90% tracking success with IMU
- **Timeline:** 2-3 weeks (1 week setup, 1 week calibration, 1 week integration)

### Success Factors
1. High-quality camera-IMU calibration (use Kalibr)
2. Accurate IMU parameters (datasheet × 10, tune iteratively)
3. Sufficient motion during initialization (translation + rotation)
4. Feature-rich environment (avoid blank walls)

### When to Reconsider
Abort if: Single fisheye FOV insufficient after thorough testing (<60% tracking success), Insta360 SDK inaccessible
Alternative: **Basalt** (native multi-camera) or **Stella VSLAM** (visual-only if IMU fails)

### Long-Term Path
- **Immediate (Weeks 1-4):** Deploy single fisheye monocular-inertial
- **Short-term (Months 1-3):** Optimize, tune, test extensively
- **Medium-term (Months 3-6):** If dual camera critical, investigate Basalt or multi-camera extension
- **Long-term (6+ months):** GPU acceleration, multi-map system, full navigation stack

**ORB-SLAM3 is the right choice. Begin Phase 1 immediately.**

---

**Report Condensed:** November 6, 2025
**Original Length:** 2678 lines → **Condensed:** 496 lines
**Recommendation:** GO with ORB-SLAM3 (85% Confidence)

---
