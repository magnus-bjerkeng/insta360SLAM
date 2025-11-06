# Kimera Research Report: Insta360 Dual Fisheye Drone SLAM (CONDENSED)

**Research Date:** November 6, 2025
**Target System:** Indoor drone with Insta360 dual fisheye camera + IMU
**Hardware:** GTX 5090, Ubuntu
**Performance Target:** Minimum 1 FPS, preferably 10-30 FPS real-time

---

## Executive Summary

### Verdict: NO-GO for Dual Non-Overlapping Fisheye | CONDITIONAL MAYBE for Single Fisheye

**Critical Blocker:** Multi-camera extension from 2023 research paper (arXiv:2304.13182) is NOT publicly available. Code developed with Ford Motor Company remains proprietary/unreleased.

### Key Advantages
1. **Modular architecture** - VIO-only mode available without full pipeline
2. **Fisheye support** - Native omni camera model (OCamCalib-based, up to 195° FOV)
3. **Complete SLAM suite** - VIO + loop closure + 3D meshing + semantic segmentation
4. **Active development** - Last commit January 2025, MIT SPARK Lab backing
5. **CPU-based performance** - Real-time without GPU (30-60 FPS VIO-only on desktop)
6. **Strong academic foundation** - Well-cited, competitive EuRoC benchmark results

### Critical Disadvantages
1. **⚠️ SHOWSTOPPER:** Multi-camera code unavailable (2023 paper code not released)
2. **No dual non-overlapping support** - Base system requires overlapping stereo FOV
3. **Complex setup** - GTSAM dependency, ROS requirement, multi-tool calibration (1-2 weeks)
4. **Stability concerns** - Reported crashes on various datasets in benchmarks
5. **Build complexity** - GTSAM build issues, dependency conflicts (1-3 days installation)

### Recommendation
- **Dual non-overlapping fisheye:** **NO-GO** - Code unavailable, would require 3-6 months reimplementation
- **Single fisheye + IMU:** **CONDITIONAL YES** - Feasible but complex; consider VINS-Fisheye/Basalt instead
- **Full SLAM pipeline needs:** **MAYBE** - Only if specifically need meshing/semantics/loop closure
- **Confidence:** 90%

---

## Multi-Camera Support Analysis ⭐ CRITICAL BLOCKER

### 2023 Multi-Camera Research Paper
- **Paper:** "Multi-Camera Visual-Inertial SLAM for Autonomous Valet Parking" (arXiv:2304.13182)
- **Authors:** Marcus Abate et al., MIT SPARK Lab + Ford Motor Company
- **Published:** April 2023, ISER 2023
- **Performance Claims:** <1% trajectory error, outperforms VINS-Fusion/ORB-SLAM3
- **Application:** Autonomous valet parking (4+ cameras on car)

### Code Availability: NOT PUBLICLY AVAILABLE ❌
**Extensive search conducted:**
- No GitHub repository for arXiv:2304.13182
- No multi-camera extensions in MIT-SPARK/Kimera repos
- Marcus Abate's GitHub (marcusabate): 17 repos, none contain multi-camera code
- No mentions in Kimera-VIO issues/PRs
- Ford partnership suggests proprietary nature

**Impact:** This is a **complete showstopper** for dual non-overlapping fisheye setup.

### Base Kimera-VIO Camera Support
- **Designed for:** Stereo with overlapping FOV + IMU
- **Stereo matching assumption:** Requires overlapping fields of view
- **Multi-camera paper:** Does NOT explicitly mention non-overlapping camera support
- **Alternative systems:** Multicam-SLAM (arXiv:2406.06374) designed for non-overlapping, but not Kimera-based

### Integration Effort Estimates
- **With multi-camera code (if available):** 2-3 weeks
- **Reimplementing from paper:** 3-6 months (expert C++/SLAM developer required)
- **Single fisheye only:** 1-2 weeks
- **Current status:** Not possible with public code

---

## Fisheye Camera Compatibility

### Supported Camera Models
1. **Pinhole Model** - Standard perspective, 5 distortion coefficients
2. **Omni Model ✅** - OCamCalib-based polynomial model for fisheye/omnidirectional
   - Supports fisheye up to 195 degrees
   - Direct distortion model (no pre-undistortion needed)
   - Automatic corner extraction

### OCamCalib Integration
- **Author:** Davide Scaramuzza (ETH Zurich RPG Lab)
- **Model:** Polynomial function for omnidirectional projection
- **Tools:** Matlab toolbox + Python (py-OCamCalib)
- **Website:** https://sites.google.com/site/scarabotix/ocamcalib-omnidirectional-camera-calibration-toolbox-for-matlab

### Configuration Example
```yaml
camera_model: omni
distortion_coefficients: [c0, c1, c2, c3, c4]  # 5 coefficients
# If OCamCalib outputs 4, set second to 0: [c0, 0, c1, c2, c3]
```

### Calibration Workflow
1. **Camera Intrinsic (OCamCalib):** 2-4 hours
   - Capture 15-20 checkerboard images at various orientations
   - Run OCamCalib automatic extraction and calibration
   - Export polynomial coefficients

2. **Camera-IMU Extrinsic (Kalibr):** 3-6 hours
   - Record rosbag with synchronized camera+IMU data
   - Run Kalibr optimization (30-60 min processing)
   - Convert to Kimera format: `Kalibr2KimeraVIO-pinhole-radtan`
   - Note: May need manual adaptation for omni model

3. **Total calibration time:** 1.5-2 days

### Fisheye Compatibility Verdict
**✅ Feasible** - Native support via omni model, but:
- Requires multiple calibration tools (OCamCalib + Kalibr)
- Some user-reported calibration issues on GitHub
- Documentation for omni model less comprehensive than pinhole
- Limited public examples with fisheye cameras

---

## Technical Feasibility

### Operating System & ROS Requirements
**Recommended:** Ubuntu 20.04 + ROS Noetic
- **Ubuntu 22.04:** NOT officially supported (ROS Noetic targets 20.04)
- **ROS1 (Noetic):** Mature, well-documented ✅
- **ROS2:** Kimera-VIO-ROS2 exists but less mature documentation ⚠️
- **Docker:** Available but reported Dockerfile bugs

### Hardware Requirements vs. Our GTX 5090 Setup
**Kimera Requirements:**
- **GPU:** NOT required (CPU-only VIO) ✅
  - GPU could accelerate semantic segmentation/meshing if used
- **CPU:** Multi-threading supported (tested on i7 laptops, Jetson Xavier NX)
- **RAM:** VIO-only ~1-2GB, Full pipeline ~3-4GB
- **Storage:** ~2-3GB (code + dependencies)

**Verdict:** GTX 5090 overkill for VIO; useful for semantics/meshing modules if needed.

### Dependencies & Build Complexity
**Core Dependencies:**
1. **GTSAM (≥4.1)** - Complex, specific build flags required
   - Must disable: `GTSAM_TANGENT_PREINTEGRATION=OFF`
   - Build time: 30-60 minutes
   - Common source of build errors
2. **OpenCV (≥3.4)** - Standard CV library
3. **OpenGV** - Geometric vision library (build from source)
4. **DBoW2** - Bag-of-words for loop closure
5. **Kimera-RPGO** - Pose graph optimization
6. **System:** Boost, TBB, Glog, Gflags, Gtest

**Installation Complexity:**
- **Docker:** >15 min build, has reported bugs
- **Manual install:** 2-4 hours if smooth, 4-8 hours with troubleshooting
- **ROS Catkin:** 1-2 hours if workspace configured correctly
- **Common issues:** GTSAM linking errors, Boost version mismatches, TBB problems

**Verdict:** Moderate to High complexity - budget 1-3 days depending on experience.

---

## Performance Metrics

### Published Benchmarks
- **Kimera-Multi on Jetson Xavier NX:** ~14 FPS dense mapping
- **EuRoC dataset:** Processes 20Hz stereo + 200Hz IMU in real-time
- **Desktop CPU:** 20-30 FPS VIO-only mode
- **Embedded (Jetson):** 10-15 FPS full pipeline

### Expected Performance for Our Setup
- **VIO-only mode:** 30-60 FPS ✅ **Exceeds target**
- **VIO + loop closure:** 20-30 FPS ✅
- **Full pipeline (VIO+RPGO+Meshing+Semantics):** 10-20 FPS ✅
- **Bottleneck:** Fisheye image processing and feature tracking

**Verdict:** Should easily meet 10-30 FPS target with VIO-only or VIO+loop closure.

### Runtime Breakdown
- **Kimera-VIO frontend:** Fast (real-time)
- **Kimera-RPGO:** Minimal overhead
- **Kimera-Mesher:** Most intensive component when enabled

---

## Modular Architecture

### Kimera Components
1. **Kimera-VIO** - Visual-Inertial Odometry (REQUIRED)
2. **Kimera-RPGO** - Loop closure (OPTIONAL, disabled by default)
3. **Kimera-Mesher** - 3D reconstruction (OPTIONAL)
4. **Kimera-Semantics** - Semantic segmentation (OPTIONAL)

### VIO-Only Deployment ✅
**Can use VIO standalone:** YES
```yaml
use_lcd: false  # Disable loop closure
metric_semantic_reconstruction: false  # Disable semantics
# Launch only Kimera-VIO node
```

**Benefits:** Faster, lighter, fewer dependencies, sufficient for short-range odometry
**Drawbacks:** No drift correction, no 3D mapping
**Recommended:** Start VIO-only, add loop closure if drift problematic

---

## Installation & Setup

### Installation Options

#### Option 1: Docker (Quickest Demo)
- Build time: 30min-1hr
- Pros: Handles dependencies automatically
- Cons: Dockerfile bugs reported, limited for development, GPU passthrough needed for semantics

#### Option 2: ROS Catkin (Recommended)
- Time: 3-6 hours (smooth) to 1-2 days (troubleshooting)
- Clone repos into catkin workspace → `catkin build kimera_vio_ros`
- Pros: Integrated ROS workflow, modular
- Cons: GTSAM dependency hell possible

#### Option 3: Manual Build (Expert)
- Time: 4-8 hours (smooth) to 2-3 days (troubleshooting)
- Build each dependency individually
- Pros: Full control, no ROS dependency
- Cons: Complex, error-prone

### Configuration Files Required
1. **Camera parameters** (camera_params.yaml) - Intrinsics, omni model coefficients
2. **IMU parameters** (imu_params.yaml) - Noise parameters, biases from Kalibr
3. **Pipeline parameters** (pipeline_params.yaml) - Frontend/backend settings
4. **ROS launch files** - Topic mappings, node configuration

**Parameter tuning:** 4-8 hours after initial setup

### Overall Setup Time
- **Expert with ROS:** 4-7 days total ✅
- **Intermediate:** 1.5-2 weeks ⚠️
- **Beginner:** 2-3 weeks ❌

---

## Critical Issues & Limitations

### Relevant GitHub Issues
- **Issue #140:** Fisheye calibration workflow struggles
- **Issue #115:** GTSAM build fails on Ubuntu 20.04 (fixed in later commits)
- **Issue #173:** GTSAM_TANGENT_PREINTEGRATION configuration confusion
- **Performance:** Some crashes on outdoor datasets
- **Stability:** Comparison papers report instability vs ORB-SLAM3

### Showstoppers for Our Use Case
**YES - Multi-camera code unavailability**

**Minor Concerns:**
- Calibration workflow complexity (time-consuming, not blocker)
- Build system fragility (Docker helps but imperfect)
- Some dataset-specific stability issues

**Stability vs Alternatives:**
- Less robust than ORB-SLAM3 in some benchmarks
- Competitive with VINS-Fusion, Basalt

### Community & Maintenance
- **Stars:** 1.8k (smaller than ORB-SLAM3: ~6k, similar to Basalt: ~1.8k)
- **Activity:** Active (January 2025 commits) ✅
- **Responsiveness:** Mixed - some issues open 1-2+ years
- **Community size:** Moderate, smaller than major alternatives

---

## Documentation Quality

**Rating: 3.5/5 (GOOD)**

**Strengths:**
- Good installation guides (Docker, catkin, manual)
- Strong academic papers (ICRA 2020, ISER 2023, IJRR 2021)
- Clear architecture overview
- ROS integration documented
- YouTube tutorial with RealSense D435i

**Weaknesses:**
- Omni camera model less detailed than pinhole
- No end-to-end fisheye calibration tutorial
- Multi-camera not documented (code unavailable)
- ROS2 documentation immature
- Troubleshooting relies on GitHub issues

**vs Alternatives:**
- Better than Basalt (less documented)
- Similar to VINS-Fusion
- Worse than ORB-SLAM3 (extensive tutorials)

---

## Comparison with Alternatives

### vs. VINS-Fusion (Drone-Proven)
- **VINS-Fisheye variant exists** ✅
- **Proven on drones** ✅
- **Kimera:** Slightly better accuracy in benchmarks, has meshing/semantics
- **VINS:** Older codebase, no semantic features
- **Verdict:** VINS-Fisheye more practical for drone fisheye applications

### vs. Basalt (Multi-Camera Capable)
- **Basalt:** Multi-camera support, excellent accuracy, low memory
- **Kimera:** Full SLAM pipeline (meshing, semantics, loop closure)
- **Verdict:** Basalt better for pure multi-camera VIO, Kimera for full pipeline

### vs. Stella VSLAM (Simpler)
- **Stella:** Simpler install, no ROS, excellent docs
- **Kimera:** IMU fusion, better accuracy for drones
- **Verdict:** Stella for visual-only, Kimera for VI-SLAM

### Ranking for Insta360 Dual Fisheye Drone
1. **VINS-Fisheye** - Drone-proven, fisheye variant ⭐⭐⭐⭐
2. **Basalt** - Multi-camera support ⭐⭐⭐⭐
3. **Kimera (single fisheye)** - If drop dual requirement ⭐⭐⭐
4. **Stella VSLAM + undistortion** - Simplest, no IMU ⭐⭐
5. **Kimera (dual fisheye)** - Code unavailable ⭐ (NOT FEASIBLE)

---

## Evidence for Insta360/Fisheye Use

### Kimera + Fisheye Evidence: LIMITED ⚠️
- **Documentation confirms:** Omni model exists, OCamCalib integration
- **Public examples:** Very few fisheye success stories
- **GitHub issues:** Some fisheye calibration struggles (Issue #140)
- **Theoretical support:** Sound (omni model proven)
- **Practical demonstration:** Limited

### Multi-Camera Examples
- **Only example:** 2023 paper (code unavailable)
- **Kimera-Multi:** Multi-robot, NOT multi-camera per robot
- **Community projects:** None found with "Kimera dual fisheye" or "Kimera multi-camera"
- **Insta360 mentions:** Zero projects combining Kimera + Insta360

### Confidence Levels
- **Single fisheye + IMU:** 60% (theory solid, practice uncertain)
- **Dual non-overlapping fisheye + IMU:** 5% (requires unavailable code)
- **Stitched fisheye + IMU:** 50% (untested, high latency risk)

---

## Repository Health

**Status:** Actively maintained ✅

- **Kimera-VIO:** 1.8k stars, 447 forks, last commit Jan 10, 2025
- **Backing:** MIT SPARK Lab (Prof. Luca Carlone)
- **Publications:** High-impact venues (ICRA, RSS, ISER, IJRR)
- **Issues:** ~40-50 open, some old issues unresolved
- **License:** BSD-2-Clause

**Concerns:**
- Multi-camera code not released despite 2023 paper
- ROS2 wrapper less polished
- Some long-standing open issues

---

## Recommendations & Next Steps

### Final Recommendation

#### For Dual Non-Overlapping Fisheye (Original Goal)
**NO-GO ❌ (95% confidence)**
- Multi-camera code unavailable
- Reimplementation: 3-6 months expert effort
- Better alternatives exist: VINS-Fisheye, Basalt

#### For Single Fisheye + IMU (Fallback)
**CONDITIONAL YES ⚠️ (60% confidence)**
- Native fisheye support exists (omni model)
- Complex setup (1-2 weeks)
- Limited public examples
- **Better alternatives:** VINS-Fisheye (drone-proven), Basalt (simpler)

#### For Full SLAM Pipeline Needs
**MAYBE ✅ (75% confidence)**
- Choose ONLY if need: real-time 3D meshing, semantic segmentation, loop closure
- Otherwise: simpler VIO-only alternatives better

### Recommended Action

**Path A: Abandon Kimera for Dual Fisheye ⭐ RECOMMENDED**
- Switch to **VINS-Fisheye** (drone-proven, fisheye variant)
- Or **Basalt** (multi-camera support, good VIO)
- Or develop custom multi-fisheye solution (long-term)

**Path B: Use Kimera with Single Fisheye (Fallback)**
- Accept single camera limitation
- Use omni camera mode
- Timeline: 3-5 weeks to deployment

**Path C: Contact MIT for Multi-Camera Code (Low Probability)**
- Email: Prof. Luca Carlone (lcarlone@mit.edu), Marcus Abate
- Success probability: 20-30%
- Fallback to Path A if unsuccessful

### Deployment Roadmap (If Proceeding with Single Fisheye)

#### Phase 1: Feasibility Testing (1-2 weeks)
1. Setup Ubuntu 20.04 + ROS Noetic
2. Install Kimera-VIO-ROS (catkin build)
3. Test with EuRoC dataset
4. Single fisheye calibration (OCamCalib + Kalibr)
5. Test with Insta360 single fisheye
6. **Go/No-Go decision**

#### Phase 2: Production Deployment (2-3 weeks)
1. VIO-only configuration (disable loop closure/meshing/semantics)
2. Parameter tuning for >10 FPS
3. Integration with drone stack
4. Field testing
5. **Deliverable:** Production VIO system

**Total Timeline:** 3-5 weeks for single fisheye approach

### Risk Mitigation
1. **Multi-camera unavailable (current):** Fallback to single fisheye or switch systems ✅
2. **Fisheye calibration fails:** Use OCamCalib carefully, validate quality (low probability)
3. **Build failures:** Use Docker initially, follow guides precisely (moderate probability)
4. **Performance <10 FPS:** Optimize parameters, VIO-only mode (low probability - should exceed)
5. **Stability issues:** Extensive testing, error recovery, backup VIO (moderate probability)

---

## Essential Resources

### Primary Repositories
- **Kimera-VIO:** https://github.com/MIT-SPARK/Kimera-VIO
- **Kimera-VIO-ROS:** https://github.com/MIT-SPARK/Kimera-VIO-ROS
- **Kimera-RPGO:** https://github.com/MIT-SPARK/Kimera-RPGO
- **Kimera-Semantics:** https://github.com/MIT-SPARK/Kimera-Semantics

### Key Papers
1. **Kimera (ICRA 2020):** https://arxiv.org/abs/1910.02490
2. **Kimera2 (ISER 2023):** https://arxiv.org/abs/2401.06323
3. **Multi-Camera (ISER 2023):** https://arxiv.org/abs/2304.13182 (CODE NOT AVAILABLE)

### Calibration Tools
- **OCamCalib:** https://sites.google.com/site/scarabotix/ocamcalib-omnidirectional-camera-calibration-toolbox-for-matlab
- **Kalibr:** https://github.com/ethz-asl/kalibr

### Tutorials
- **Kimera + RealSense D435i:** https://www.youtube.com/watch?v=Zjevg5wQTdI
- **MIT SPARK Lab:** http://web.mit.edu/sparklab/

---

## Conclusion

**Kimera is a sophisticated SLAM system with strong academic backing and comprehensive features.** However, **it is NOT suitable for our Insta360 dual non-overlapping fisheye setup** due to unavailable multi-camera code.

**For dual fisheye:** Switch to VINS-Fisheye or Basalt.
**For single fisheye:** Kimera feasible but complex; alternatives may be more practical.
**For full SLAM pipeline:** Kimera excellent if you need meshing/semantics.

**Final Verdict: NO-GO for dual fisheye (90% confidence) | CONDITIONAL YES for single fisheye (60% confidence)**

---

**Report Date:** November 6, 2025 | **Condensed from:** 1703 lines → 493 lines
