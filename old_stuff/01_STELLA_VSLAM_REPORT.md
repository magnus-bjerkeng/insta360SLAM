# Stella VSLAM Research Report for Insta360 Dual Fisheye Drone SLAM

**Research Date:** November 6, 2025
**Project:** Indoor Drone SLAM with Insta360 Dual Fisheye Camera + IMU
**Hardware:** Ubuntu + GTX 5090
**Researcher:** AI Research Agent

---

## 1. Executive Summary

### Overall Assessment

Stella VSLAM is a **strong candidate** for the Insta360 dual fisheye drone SLAM project with a **CONDITIONAL GO** recommendation. The system provides native support for equirectangular (360°) camera models, has active community development, and includes Docker deployment options that simplify setup on Ubuntu with GPU acceleration.

### Key Strengths

1. **Native Equirectangular Support**: Stella VSLAM explicitly supports equirectangular camera models and has been demonstrated with Insta360 cameras in the community (Discussion #158 with Insta360 ONE X2)
2. **Active Development**: Repository has 1.1k stars, 443 forks, 55 contributors, and recent commits indicate ongoing maintenance
3. **Docker + GPU Support**: Multiple Docker configurations available with NVIDIA CUDA support, reducing setup complexity
4. **Proven UAV Fork**: RoblabWh/stella_vslam_dense fork specifically optimized for 360° action cams on small UAVs with real-time performance
5. **Permissive License**: 2-clause BSD license allows commercial use
6. **Comprehensive Documentation**: Well-maintained documentation at stella-cv.readthedocs.io with tutorials and examples
7. **Real-time Performance**: Achieves ~0.045s median tracking time (22 FPS equivalent) on desktop hardware

### Key Weaknesses

1. **No Native IMU Integration**: Visual-only SLAM; IMU integration is on the roadmap but not yet implemented (would require external loose coupling)
2. **High Build Complexity**: Manual build requires 30-60 minutes with many dependencies (Eigen, g2o, OpenCV, FBoW, SuiteSparse)
3. **Stitching Required**: Must stitch dual fisheye to equirectangular before SLAM processing (adds preprocessing latency)
4. **Resolution Sensitivity**: Community reports suggest lowering resolution from 5.7K to 4K improves tracking consistency
5. **No Direct Dual Fisheye Mode**: Cannot use two fisheye lenses independently; must stitch first

### Go/No-Go Recommendation

**CONDITIONAL GO - Medium-High Confidence (75%)**

**Proceed if:**
- You can implement real-time stitching from Insta360 dual fisheye → 4K equirectangular at 10-30 FPS
- You accept visual-only SLAM initially (IMU can be added via loose coupling externally)
- You use Docker deployment to avoid build complexity
- You're prepared for 1-2 weeks of initial setup and calibration

**Do NOT proceed if:**
- You require tightly-coupled visual-inertial SLAM from day one (consider ORB-SLAM3 or VINS-Fusion instead)
- You cannot achieve real-time stitching (Stella VSLAM needs stitched frames as input)
- You need to process both fisheye streams independently without stitching

---

## 2. Technical Feasibility

### 2.1 Hardware Compatibility

| Component | Compatibility | Details |
|-----------|---------------|---------|
| **Operating System** | ✅ Excellent | Ubuntu 20.04, 22.04 officially supported and tested |
| **GPU (GTX 5090)** | ✅ Excellent | NVIDIA GPU support via CUDA 11.8+, Docker GPU passthrough with `--gpus all` |
| **CPU** | ✅ Good | Tested on Intel Core i7-7820HK (2.90GHz, 4C8T), modern CPUs will perform better |
| **RAM** | ✅ Adequate | 32GB recommended for high-resolution processing; HD needs 16GB minimum |

### 2.2 Dependencies

**Core Dependencies:**
- Eigen (v3.3.0+)
- g2o (20230223_git or later)
- SuiteSparse
- FBoW (custom stella-cv version)
- yaml-cpp (v0.6.0+)
- OpenCV (v3.3.1+ with CUDA support)
- C++11-compliant compiler

**Optional but Recommended:**
- backward-cpp (stack-trace logging)
- Pangolin/Iridescence viewer
- NVIDIA CUDA Toolkit (for GPU acceleration)

### 2.3 Expected Performance

**Desktop Performance (Based on Benchmarks):**
- **Tracking Time**: ~0.045s median (22 FPS equivalent) on equirectangular 1920x960 @ 30fps
- **CPU Load**: Average 230.6% (max 310.5%) on quad-core Intel i5-6600
- **Memory Usage**: Average 2.6GB (max 4.4GB) on outdoor sequences
- **GPU Acceleration**: Available for ORB feature extraction via cuda-efficient-features

**Expected on GTX 5090 + Modern CPU:**
- **Estimated FPS**: 20-30 FPS for 4K equirectangular input (based on scaling from benchmarks)
- **Minimum Viable**: 10+ FPS achievable, exceeding 1 FPS requirement
- **Optimization**: Frame-skip (process every 3rd frame) can increase throughput if needed

### 2.4 Estimated Setup Time

**Docker Route (Recommended):**
- **Day 1**: Pull/build Docker image (~2-3 hours including downloads)
- **Day 2-3**: Configure Insta360 stitching pipeline, test with sample data
- **Day 4-7**: Calibration, parameter tuning, integration testing
- **Total**: 1 week to working prototype

**Build from Source:**
- **Day 1-2**: Install dependencies, build libraries (Eigen, g2o, OpenCV with CUDA)
- **Day 3**: Build Stella VSLAM and viewer
- **Day 4-7**: Same as Docker route
- **Total**: 1-1.5 weeks

**Deployment Complexity**: **Moderate** with Docker, **High** without Docker

---

## 3. Repository Health

### 3.1 GitHub Statistics

| Metric | Value | Assessment |
|--------|-------|------------|
| **Stars** | 1,100+ | Good community interest |
| **Forks** | 443 | High adoption, active derivatives |
| **Contributors** | 55 | Healthy community contribution |
| **Commits** | 1,061+ | Mature codebase |
| **License** | 2-clause BSD | Permissive, commercial-friendly |
| **CI Status** | ✅ Passing | Active testing infrastructure |
| **Last Update** | Recent (2024) | Actively maintained |

### 3.2 Maintenance Status

**Active Development**: Yes
- Regular commits and releases
- Responsive maintainers in GitHub Discussions
- CI/CD pipeline with automated testing
- Documentation updates synchronized with code changes

**Current Priorities** (from README):
1. Refactoring
2. Algorithm changes and parameter additions to improve performance
3. Add tests
4. Marker integration
5. Implementation of extra camera models
6. Python bindings
7. **IMU integration** (planned but not yet implemented)

### 3.3 Community Engagement

**GitHub Discussions**: Active forum for questions and support
**Issue Response Time**: Maintainers respond within days to weeks
**Documentation**: Comprehensive with readthedocs site
**Examples**: Multiple example datasets and configuration files provided

**Community Size**: Medium-sized active community with expertise in omnidirectional SLAM

---

## 4. Critical Issues

### 4.1 Relevant Open Issues & Discussions

| Issue/Discussion | Relevance | Status | Impact |
|------------------|-----------|--------|--------|
| **#158: Insta360 ONE X2 Parameters** | ⭐⭐⭐ Critical | Resolved | Recommends 4K resolution instead of 5.7K for better consistency |
| **#245: Android Performance** | ⚠️ Low | Open | Only 2 FPS on mobile (not relevant for desktop) |
| **#242: Marker Integration** | ✅ Feature | Open | Could enable scale correction for equirectangular |
| **#168: Cross-Camera Localization** | ✅ Info | Resolved | Confirms localization works with different equirectangular cameras |
| **#73: CPU Usage** | ⚠️ Medium | Discussed | OpenVSLAM uses significant CPU; GPU acceleration helps |

**Note**: Original OpenVSLAM repo (xdspacelab/openvslam) is archived, but relevant issues include:
- **#427**: Equirectangular video tracking failures → resolved by adjusting parameters
- **#78**: Insta360 One X configuration → confirmed to work with parameter adjustments

### 4.2 Showstopper Analysis

**No Critical Blockers Identified** for our use case, but important considerations:

1. **Resolution Tuning Required**: Community experience shows Insta360 ONE X2 at 5760x2880 has inconsistent tracking; recommended to downscale to 4K (3840x2160) for better performance
2. **IMU Not Native**: If tightly-coupled visual-inertial SLAM is mandatory, Stella VSLAM is NOT the right choice
3. **Stitching Latency**: Pre-processing latency for dual fisheye → equirectangular stitching must be factored into real-time budget

### 4.3 Community Responsiveness

**Good**: Maintainers actively respond to issues and discussions. The Discussion #158 about Insta360 ONE X2 received helpful guidance from maintainers, demonstrating community support for 360° camera users.

---

## 5. Documentation Assessment

### 5.1 Quality Rating: **GOOD** (4/5)

### 5.2 Available Documentation

| Documentation Type | Quality | Notes |
|--------------------|---------|-------|
| **README** | ✅ Excellent | Comprehensive overview, clear features, installation links |
| **Installation Guide** | ✅ Excellent | Step-by-step for Ubuntu, macOS, Windows WSL, Docker |
| **Docker Guide** | ✅ Excellent | Multiple Docker configs with GPU support instructions |
| **Simple Tutorial** | ✅ Excellent | Downloadable datasets, example configs, clear steps |
| **Example Configs** | ✅ Excellent | Equirectangular, fisheye, perspective configs included |
| **API Docs** | ⚠️ Basic | Code is well-structured but API documentation is limited |
| **ROS Integration** | ✅ Good | Separate stella_vslam_ros repo with ROS2 support |
| **Camera Calibration** | ⚠️ Fair | Not extensively documented; requires OpenCV knowledge |

### 5.3 What's Well Documented

- Installation process (native and Docker)
- Example datasets for testing (equirectangular datasets provided via Google Drive)
- Configuration file format and parameters
- Running SLAM with different camera models
- Map saving/loading functionality
- Benchmarking with KITTI, EuRoC, TUM-RGBD datasets

### 5.4 What's Missing or Unclear

- Detailed camera calibration procedures for 360° cameras
- Performance tuning guide for high-resolution inputs
- Real-time integration patterns (live camera streaming)
- IMU integration approaches (since it's not native)
- Stitching pipeline recommendations for dual fisheye cameras
- Troubleshooting guide for common issues

### 5.5 Community Tutorials

- **Official Examples Repo**: https://github.com/stella-cv/stella_vslam_examples
- **Academic Paper**: "OpenVSLAM: A Versatile Visual SLAM Framework" (ACM MM 2019, won 1st place in Open Source Competition)
- **Dense Reconstruction Paper**: 2022 SSRR conference paper for stella_vslam_dense
- Limited third-party tutorials found

---

## 6. Deployment Options

### 6.1 Docker Availability and Quality

**Docker Support**: ✅ **Excellent**

#### Available Docker Images

1. **Dockerfile.cuda** - CUDA-enabled with GPU acceleration (RECOMMENDED for our use case)
   - Base: nvidia/cuda:11.8.0-devel-ubuntu22.04
   - Includes: OpenCV with CUDA, cuda-efficient-features, Pangolin viewer
   - GPU Support: `--gpus all` flag for NVIDIA GPUs

2. **Dockerfile.iridescence** - Modern viewer (Ubuntu 20.04)
   - Includes IridescenceViewer
   - Requires X11 forwarding for GUI

3. **Dockerfile.desktop** - Pangolin viewer (Ubuntu 20.04)
   - Traditional Pangolin-based viewer
   - Does NOT support Docker for Mac

4. **Dockerfile.socket** - Web-based viewer
   - Cross-platform compatible
   - Accessible via browser at localhost:3001

#### Docker GPU Support

- **Requirements**: NVIDIA driver v390+, nvidia-docker2 installed
- **Command**: `docker run -it --rm --gpus all -e DISPLAY=$DISPLAY ...`
- **Performance**: Full GPU acceleration for ORB feature extraction and visualization

#### Build Time

- **Standard Build**: ~1-2 hours (downloads + compilation)
- **Parallel Build**: Use `--build-arg NUM_THREADS=$(nproc)` to speed up
- **Disk Space**: ~5-8GB for full image with dependencies

### 6.2 Pre-built Images

**Docker Hub**:
- Community image available: `ubeike/stella-vslam`
- **Status**: Not officially maintained by stella-cv team; recommend building from official Dockerfiles

### 6.3 Build-from-Source Complexity

**Complexity**: **HIGH**

**Steps Required**:
1. Install system dependencies (~20 packages via apt)
2. Build Eigen from source
3. Build OpenCV from source (with CUDA support adds complexity)
4. Build g2o from source
5. Build FBoW from source
6. Build backward-cpp (optional)
7. Build Stella VSLAM
8. Build viewer (Pangolin/Iridescence/Socket)

**Estimated Time**: 30-60 minutes on modern hardware
**Disk Space**: ~10GB for source + build artifacts
**Failure Risk**: Medium (dependency version mismatches possible)

### 6.4 Recommendation

**Use Docker with CUDA support** (`Dockerfile.cuda`) for:
- Faster deployment (1 day vs 3+ days)
- Reproducible environment
- GPU acceleration out-of-the-box
- Easier troubleshooting (consistent environment)

---

## 7. Insta360 Compatibility ⭐ CRITICAL

### 7.1 Evidence of Insta360 Usage

**✅ CONFIRMED**: Stella VSLAM has been successfully used with Insta360 cameras.

#### Primary Evidence

1. **GitHub Discussion #158**: "How to adjust parameters for Insta360 ONE X2?"
   - **URL**: https://github.com/stella-cv/stella_vslam/discussions/158
   - **Camera**: Insta360 ONE X2
   - **Resolution**: 5760x2880 @ 30fps (original), 4K recommended
   - **Outcome**: Successful tracking after parameter tuning
   - **Key Recommendation**: Lower resolution to 4K for better consistency

2. **Official README Statement**:
   > "visual SLAM algorithm using **equirectangular camera models** (e.g. RICOH THETA series, **insta360 series**, etc)"

3. **stella_vslam_dense Fork for UAVs**:
   - **URL**: https://github.com/RoblabWh/stella_vslam_dense
   - **Purpose**: "Real-time 3D reconstruction for 360° action cams on small UAVs"
   - **Tested With**: "DJI Avata with Insta360 modules, GoPro Max"
   - **Performance**: Real-time processing on HD equirectangular video
   - **Publication**: 2022 SSRR conference paper

#### Archived OpenVSLAM Issues (Pre-Fork)

1. **Issue #78**: "insta360 one X camera model"
   - Confirmed equirectangular model works with Insta360 One X
   - Advised changing Camera.cols and Camera.rows to match resolution

2. **Issue #427**: "360 equirectangular video fails to track"
   - Troubleshooting for equirectangular tracking
   - Resolved by increasing max_num_keypoints parameter

### 7.2 Configuration Examples

#### Insta360 ONE X2 Configuration (from Discussion #158)

```yaml
Camera:
  name: "Insta360 ONE X2"
  setup: "monocular"
  model: "equirectangular"

  fps: 30.0
  cols: 5760  # Original (recommended: 3840 for 4K)
  rows: 2880  # Original (recommended: 2160 for 4K)

  color_order: "RGB"

Feature:
  max_num_keypoints: 5000  # May need adjustment
  scale_factor: 1.2
  num_levels: 8
  ini_fast_threshold: 20
  min_fast_threshold: 7
```

**Maintainer Recommendation**:
> "I recommend that you try lowering the resolution by resizing."

### 7.3 Recommended Stitching Approach

**Option 1: Insta360 SDK (RECOMMENDED)**
- **Pros**: Hardware-optimized, lowest latency, best quality
- **Cons**: Proprietary, licensing required
- **Expected Latency**: <10ms for real-time stitching

**Option 2: OpenCV Stitcher**
- **Pros**: Open-source, customizable
- **Cons**: Slower than SDK, requires calibration
- **Expected Latency**: 50-200ms depending on resolution

**Option 3: Pre-computed Lookup Tables**
- **Pros**: Fastest after initial calibration
- **Cons**: Requires extrinsic calibration between cameras
- **Expected Latency**: 5-15ms

**Recommendation for Real-time**: Use Insta360 SDK for stitching → output 4K equirectangular @ 30fps → feed to Stella VSLAM

### 7.4 Dual Fisheye vs Omnidirectional Decision

| Approach | Feasibility | Pros | Cons | Recommendation |
|----------|-------------|------|------|----------------|
| **Stitch to Equirectangular** | ✅ Proven | Native support, existing configs, community experience | Adds stitching latency, loses some FOV | ✅ **RECOMMENDED** |
| **Single Fisheye (Front/Back)** | ✅ Possible | No stitching, simpler pipeline | Reduced FOV, no 360° awareness | ⚠️ Fallback option |
| **Dual Fisheye Stereo** | ❌ Not Supported | Best 3D reconstruction | No native support in Stella VSLAM | ❌ Not feasible |
| **Dual Independent Monocular** | ⚠️ Theoretical | Two separate maps | Map fusion complexity, not tested | ❌ Not recommended |

### 7.5 Resolution Recommendations

Based on community experience and maintainer guidance:

| Resolution | FPS Target | Tracking Quality | Performance | Recommendation |
|------------|------------|------------------|-------------|----------------|
| **8K (7680x3840)** | <10 FPS | Unknown | Too demanding | ❌ Not viable |
| **5.7K (5760x2880)** | 10-20 FPS | Inconsistent | Demanding | ⚠️ Avoid |
| **4K (3840x2160)** | 20-30 FPS | Good | Balanced | ✅ **RECOMMENDED** |
| **2K (2560x1280)** | 30+ FPS | Good | Fast | ✅ Alternative if FPS critical |
| **HD (1920x960)** | 30+ FPS | Good | Very Fast | ⚠️ Lower spatial resolution |

**Optimal Choice**: **4K (3840x2160) @ 30fps** for balance of quality and performance

---

## 8. Camera Input Analysis

### 8.1 Supported Camera Models

Stella VSLAM implements **4 camera models** in `/src/stella_vslam/camera/`:

1. **perspective.h/cc** - Standard pinhole camera model
   - Parameters: fx, fy, cx, cy, k1, k2, p1, p2, k3

2. **fisheye.h/cc** - Fisheye lens model (Kannala-Brandt)
   - Parameters: fx, fy, cx, cy, k1, k2, k3, k4
   - **Compatible with**: Single fisheye lenses (e.g., LUMIX 8mm fisheye)
   - **Stereo Support**: Yes, for stereo fisheye pairs

3. **equirectangular.h/cc** - 360° omnidirectional model ⭐
   - Parameters: cols, rows, fps (no intrinsics needed)
   - **Compatible with**: RICOH THETA, Insta360 (stitched output)
   - **Projection**: Latitude-longitude mapping

4. **radial_division.h/cc** - Radial division model
   - Alternative distortion model
   - Less commonly used

### 8.2 Best Approach for Insta360 Dual Fisheye

**Recommended Pipeline**:

```
Insta360 Dual Fisheye (Front + Back)
    ↓
[Real-time Stitching: Insta360 SDK or OpenCV]
    ↓
Equirectangular 4K @ 30fps (3840x2160, RGB)
    ↓
[Stella VSLAM: equirectangular camera model]
    ↓
SLAM Output (pose, map, trajectory)
```

**Configuration Requirements**:

1. **Camera Calibration**:
   - Equirectangular model requires NO intrinsic calibration
   - Only need to specify image dimensions (cols, rows)
   - Assumes perfect equirectangular projection from stitching

2. **Extrinsic Calibration**:
   - Between front and back fisheye (for stitching only)
   - NOT required by Stella VSLAM (works on stitched image)

3. **Configuration File**:
   - Use `example/aist/equirectangular.yaml` as template
   - Modify cols/rows to match Insta360 output resolution
   - Tune ORB feature parameters based on scene texture

### 8.3 Alternative: Single Fisheye Mode

If stitching latency is prohibitive:

**Use Front Fisheye Only**:
- Camera model: `fisheye`
- Requires intrinsic calibration (fx, fy, cx, cy, k1-k4)
- FOV: ~180-220° (vs 360° for stitched)
- Performance: Slightly faster (no stitching overhead)
- Trade-off: Reduced situational awareness for drone

**Configuration**: Use `example/aist/fisheye.yaml` as template

### 8.4 Why NOT Dual Fisheye Stereo?

Stella VSLAM's stereo mode expects:
- Two perspective or fisheye cameras with **overlapping FOV**
- Rectified stereo pair for depth estimation

Insta360 dual fisheye has:
- **Non-overlapping FOV** (front: 0-180°, back: 180-360°)
- Cannot be used as stereo pair

**Conclusion**: Dual fisheye stereo mode is NOT feasible with Insta360 configuration

---

## 9. Comparison with Dense Fork

### 9.1 Main Stella VSLAM vs RoblabWh/stella_vslam_dense

| Feature | Main Stella VSLAM | stella_vslam_dense |
|---------|-------------------|---------------------|
| **Purpose** | Sparse feature-based SLAM | Sparse SLAM + Dense 3D Reconstruction |
| **UAV Focus** | General-purpose | **Optimized for UAVs with 360° cams** |
| **Dense Mapping** | ❌ No | ✅ Yes (PatchMatch-Stereo) |
| **GPU Acceleration** | ORB feature extraction | ORB + Dense reconstruction |
| **Memory Usage** | ~2-4GB | ~16GB (HD), 32GB+ (5.7K) |
| **Performance** | 20-30 FPS (4K, sparse only) | Real-time with frame-skip 3 (HD) |
| **Maintenance** | Active (stella-cv) | Active (RoblabWh) |
| **Stars/Forks** | 1.1k / 443 | 41 / 443 |
| **Documentation** | Excellent | Good |
| **Input Modes** | Mono, Stereo, RGBD | **Mono equirectangular only** |

### 9.2 Pros of Main Stella VSLAM

1. **Broader Use Case**: Supports perspective, fisheye, equirectangular, stereo, RGBD
2. **Lower Memory**: Works with 8-16GB RAM
3. **Mature Codebase**: More contributors, longer development history
4. **Better Documentation**: Comprehensive readthedocs site
5. **Flexibility**: Can switch camera models easily

### 9.3 Pros of stella_vslam_dense

1. **UAV-Specific**: Explicitly designed for 360° drones
2. **Dense Reconstruction**: Produces dense 3D point clouds (useful for obstacle avoidance)
3. **Proven on UAVs**: Tested with "DJI Avata with Insta360 modules"
4. **Academic Publication**: 2022 SSRR conference paper validates approach
5. **Optimized for Equirectangular**: PatchMatch-Stereo adapted for latitude-longitude projection

### 9.4 Cons of Each

**Main Stella VSLAM**:
- Sparse map only (may not be sufficient for dense obstacle avoidance)
- No UAV-specific optimizations

**stella_vslam_dense**:
- Smaller community (41 stars vs 1.1k)
- Higher memory requirements (32GB+ for 5.7K)
- Limited to monocular equirectangular only
- Specialized use case (less flexible)

### 9.5 Recommendation

**Start with Main Stella VSLAM**, then consider stella_vslam_dense if:

| Scenario | Recommendation | Reason |
|----------|----------------|--------|
| **Proof-of-Concept / MVP** | Main Stella VSLAM | Broader support, better docs, lower resource requirements |
| **Production Drone System** | Main Stella VSLAM | More mature, flexible for future camera changes |
| **Need Dense Reconstruction** | stella_vslam_dense | Only if dense 3D mapping is critical for obstacle avoidance |
| **Academic Research** | stella_vslam_dense | Published method, UAV-specific optimizations |
| **Tight Memory Budget** | Main Stella VSLAM | Works with 8-16GB RAM |

**Hybrid Approach**: Use main Stella VSLAM for localization/mapping, optionally add offline dense reconstruction with stella_vslam_dense fork for map post-processing.

---

## 10. Next Steps

### If We Proceed with Stella VSLAM

#### Phase 1: Environment Setup (Week 1)

**Day 1-2: Docker Environment**
- [ ] Install nvidia-docker2 on Ubuntu
- [ ] Clone stella_vslam repository
- [ ] Build Docker image using `Dockerfile.cuda`
- [ ] Verify GPU access with `docker run --gpus all nvidia-smi`
- [ ] Download ORB vocabulary file (orb_vocab.fbow)

**Day 3-4: Test with Sample Data**
- [ ] Download AIST equirectangular dataset (aist_living_lab_1)
- [ ] Run SLAM with example config: `equirectangular.yaml`
- [ ] Verify tracking, mapping, and visualization work
- [ ] Benchmark performance on sample data

**Day 5-7: Insta360 Integration**
- [ ] Implement stitching pipeline (Insta360 SDK or OpenCV)
- [ ] Capture test footage with Insta360 in target environment
- [ ] Stitch dual fisheye → 4K equirectangular
- [ ] Create Insta360-specific config file (based on Discussion #158)

#### Phase 2: Calibration & Tuning (Week 2)

**Day 8-10: Parameter Tuning**
- [ ] Tune ORB feature extraction parameters:
  - `max_num_keypoints`: Start with 5000, adjust based on scene
  - `scale_factor`: 1.2 (default)
  - `num_levels`: 8 (default)
  - `ini_fast_threshold` / `min_fast_threshold`: Adjust for texture density
- [ ] Test different resolutions: 4K, 2K, HD
- [ ] Optimize frame-skip parameter if FPS target not met
- [ ] Configure masking rectangles (top/bottom/sides) if needed

**Day 11-12: Performance Optimization**
- [ ] Enable GPU acceleration for ORB extraction
- [ ] Profile stitching latency (should be <50ms)
- [ ] Profile SLAM tracking time (target <0.05s for 20 FPS)
- [ ] Test loop closure detection in indoor environment

**Day 13-14: Integration Testing**
- [ ] End-to-end test: Insta360 live → stitching → SLAM
- [ ] Measure total latency (stitching + SLAM)
- [ ] Verify map consistency over multiple flights
- [ ] Test map save/load functionality

#### Phase 3: IMU Integration (Week 3-4, if needed)

**Option A: External Loose Coupling**
- [ ] Implement separate IMU preintegration (e.g., using GTSAM)
- [ ] Fuse Stella VSLAM pose estimates with IMU data offline
- [ ] Evaluate improvement in trajectory smoothness

**Option B: Fork for Tight Coupling**
- [ ] Fork stella_vslam repository
- [ ] Integrate IMU preintegration into tracking thread (significant effort)
- [ ] Add IMU-visual bundle adjustment (weeks of development)

**Recommendation**: Start without IMU; add loose coupling only if visual-only tracking proves insufficient

#### Phase 4: Deployment & Testing (Week 4+)

- [ ] Package Docker container with all dependencies
- [ ] Create launch scripts for automated startup
- [ ] Conduct extensive flight tests in target environments
- [ ] Evaluate robustness to lighting changes, motion blur, texture-less areas
- [ ] Document failure modes and recovery strategies

### Estimated Timeline to Working System

| Milestone | Duration | Total Time |
|-----------|----------|------------|
| Docker setup + sample data testing | 3-4 days | 4 days |
| Insta360 stitching integration | 3-4 days | 1 week |
| Calibration & parameter tuning | 5-7 days | 2 weeks |
| Integration testing & optimization | 3-5 days | 2.5 weeks |
| **First Working Prototype** | - | **2-3 weeks** |
| IMU integration (optional) | 1-2 weeks | 4-5 weeks |
| Production hardening | 1-2 weeks | 5-7 weeks |

**Fastest Path to Prototype**: 2 weeks
**Conservative Estimate**: 3-4 weeks
**With IMU Integration**: 5-7 weeks

### What to Prepare

**Before Starting**:
1. **Insta360 Camera Calibration**: Obtain or perform extrinsic calibration between front and back fisheye lenses
2. **Stitching Library**: Choose between Insta360 SDK (requires license) or OpenCV (open-source)
3. **Test Environment**: Prepare indoor flight space with good texture (avoid texture-less walls)
4. **Sample Datasets**: Collect 5-10 minutes of Insta360 footage in target environment for offline testing

**Hardware Preparation**:
1. Ensure Ubuntu system has latest NVIDIA drivers (v390+)
2. Install Docker and nvidia-docker2
3. Allocate 50GB+ disk space for Docker images and datasets
4. Verify GTX 5090 is recognized and functional

**Software Preparation**:
1. Familiarize with Stella VSLAM documentation: https://stella-cv.readthedocs.io/
2. Review Discussion #158 for Insta360-specific guidance
3. Study example configuration files in `/example/aist/`
4. Clone and explore stella_vslam repository locally

### Potential Risks and Mitigation Strategies

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| **Stitching latency too high** | Medium | High | Use Insta360 SDK instead of OpenCV; consider hardware acceleration |
| **Tracking fails in low-texture areas** | Medium | Medium | Tune ORB parameters; add artificial texture markers if needed |
| **4K processing too slow** | Low | Medium | Use 2K resolution; enable frame-skip; GPU acceleration |
| **Loop closure false positives** | Low | Medium | Tune vocabulary parameters; increase `min_distance_on_graph` |
| **Memory exhaustion on long flights** | Low | Medium | Implement map pruning; limit max keyframes |
| **IMU required for stability** | High | High | Plan for external loose coupling; consider alternative SLAM if critical |

### Success Criteria

The deployment is successful if:
- ✅ Achieves ≥10 FPS end-to-end (stitching + SLAM)
- ✅ Tracks consistently for ≥5-minute indoor flights
- ✅ Loop closure works reliably in revisited areas
- ✅ Map quality sufficient for localization and path planning
- ✅ Pose estimation error <5% of traveled distance (without loop closure)
- ✅ System recovers gracefully from tracking loss

---

## 11. Resource Links

### Official Repositories

- **Main Stella VSLAM**: https://github.com/stella-cv/stella_vslam
- **Dense Reconstruction Fork**: https://github.com/RoblabWh/stella_vslam_dense
- **ROS2 Wrapper**: https://github.com/stella-cv/stella_vslam_ros
- **Examples Repository**: https://github.com/stella-cv/stella_vslam_examples
- **ORB Vocabulary**: https://github.com/stella-cv/FBoW_orb_vocab
- **Original OpenVSLAM (archived)**: https://github.com/xdspacelab/openvslam

### Documentation

- **Official Documentation**: https://stella-cv.readthedocs.io/
- **Installation Guide**: https://stella-cv.readthedocs.io/en/latest/installation.html
- **Docker Guide**: https://stella-cv.readthedocs.io/en/latest/docker.html
- **Simple Tutorial**: https://stella-cv.readthedocs.io/en/latest/simple_tutorial.html
- **Examples**: https://stella-cv.readthedocs.io/en/latest/example.html
- **ROS2 Package Guide**: https://stella-cv.readthedocs.io/en/latest/ros2_package.html

### Academic Papers

- **OpenVSLAM Paper** (ACM MM 2019, 1st place winner):
  - arXiv: https://arxiv.org/abs/1910.01122
  - DOI: 10.1145/3343031.3350539
  - Citation: Sumikura et al., "OpenVSLAM: A Versatile Visual SLAM Framework"

- **stella_vslam_dense Paper** (SSRR 2022):
  - Conference: IEEE International Symposium on Safety, Security, and Rescue Robotics
  - Topic: Real-time dense reconstruction for UAV 360° cameras

### GitHub Discussions & Issues (Insta360-Relevant)

- **Discussion #158**: Insta360 ONE X2 parameters
  - https://github.com/stella-cv/stella_vslam/discussions/158
  - **Key Takeaway**: Use 4K resolution, tune max_num_keypoints

- **Discussion #168**: Cross-camera localization
  - https://github.com/stella-cv/stella_vslam/discussions/168

- **Discussion #245**: Android performance issues
  - https://github.com/stella-cv/stella_vslam/discussions/245

- **Issue #242**: Marker integration for equirectangular
  - https://github.com/stella-cv/stella_vslam/issues/242

- **Discussion #73**: CPU usage concerns
  - https://github.com/stella-cv/stella_vslam/discussions/73

### Archived OpenVSLAM Issues (Still Relevant)

- **Issue #78**: Insta360 One X camera model
  - https://github.com/xdspacelab/openvslam/issues/78

- **Issue #427**: 360 equirectangular video tracking troubleshooting
  - https://github.com/xdspacelab/openvslam/issues/427

### Docker Resources

- **Docker Hub (Community)**: https://hub.docker.com/r/ubeike/stella-vslam
- **NVIDIA CUDA Base Images**: https://hub.docker.com/r/nvidia/cuda
- **nvidia-docker2 Installation**: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html

### Sample Datasets

- **AIST Datasets** (Equirectangular):
  - Google Drive: https://drive.google.com/open?id=1A_gq8LYuENePhNHsuscLZQPhbJJwzAq4
  - Includes: Living lab, factory, entrance hall scenes
  - Resolution: 1920x960 @ 30fps

- **Standard SLAM Benchmarks**:
  - KITTI Odometry: http://www.cvlibs.net/datasets/kitti/eval_odometry.php
  - EuRoC MAV: https://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets
  - TUM RGB-D: https://vision.in.tum.de/data/datasets/rgbd-dataset

### Community & Support

- **GitHub Discussions**: https://github.com/stella-cv/stella_vslam/discussions
- **Issues Tracker**: https://github.com/stella-cv/stella_vslam/issues
- **r/computervision**: Reddit community for general SLAM questions
- **r/robotics**: Reddit community for robotics/drone applications

### Related Projects

- **ORB-SLAM3**: https://github.com/UZ-SLAMLab/ORB_SLAM3
  - Alternative with tightly-coupled IMU, but no equirectangular support

- **VINS-Fusion**: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion
  - Visual-inertial SLAM, but no equirectangular support

- **OpenVINS**: https://github.com/rpng/open_vins
  - Modern visual-inertial estimator, no equirectangular

- **NVIDIA Isaac ROS Visual SLAM**: https://github.com/NVIDIA-ISAAC-ROS/isaac_ros_visual_slam
  - GPU-accelerated, but perspective cameras only

### Helpful Blog Posts & Tutorials

- **Samsung Research Blog**: Benchmarking Stella SLAM
  - https://research.samsung.com/blog/Open-source-open-hardware-ground-truth-for-Visual-Odometry-and-SLAM-applications

- **360VO Research**: 360° Visual Odometry (related work)
  - Paper: https://www.saikit.org/static/projects/360vo_2022/360VO_ICRA2022.pdf

---

## 12. Final Recommendations

### Primary Recommendation: CONDITIONAL GO

**Proceed with Stella VSLAM if and only if:**

1. ✅ **Stitching is Viable**: You can achieve real-time stitching (Insta360 SDK or GPU-accelerated OpenCV) at 10-30 FPS
2. ✅ **Visual-Only is Acceptable**: IMU can be loosely coupled externally; tight coupling not required initially
3. ✅ **Docker Deployment**: Use containerized deployment to minimize setup complexity
4. ✅ **4K Resolution**: Plan to process 4K equirectangular (not full 5.7K) based on community recommendations
5. ✅ **2-3 Week Timeline**: Acceptable time-to-prototype for initial testing

### Alternative Recommendations

If Stella VSLAM proves unsuitable, consider:

**For Tightly-Coupled Visual-Inertial:**
- **ORB-SLAM3**: Best visual-inertial SLAM, but would need to use single fisheye (no equirectangular)
- **VINS-Fusion**: Good VIO performance, but perspective/fisheye only

**For Better Performance:**
- **NVIDIA Isaac ROS Visual SLAM**: GPU-accelerated, but perspective cameras only (would need single fisheye)

**For Production Systems:**
- **Commercial Solutions**: Consider Insta360's own SLAM SDK or third-party solutions (e.g., Slamtec, Intel RealSense)

### Decision Tree

```
START
  ↓
Can you stitch dual fisheye → equirectangular at 10+ FPS?
  ├─ YES → Continue
  └─ NO → Consider single fisheye with ORB-SLAM3
           ↓
Do you REQUIRE tightly-coupled IMU from day one?
  ├─ NO → Stella VSLAM ✅ (RECOMMENDED)
  └─ YES → ORB-SLAM3 with single fisheye
           ↓
Is 2-3 week setup timeline acceptable?
  ├─ YES → Stella VSLAM ✅ (RECOMMENDED)
  └─ NO → Consider commercial solutions
```

### Confidence Assessment

| Aspect | Confidence Level | Reasoning |
|--------|------------------|-----------|
| **Technical Feasibility** | ✅ High (85%) | Proven with Insta360, good documentation, active community |
| **Performance Target** | ✅ High (80%) | Benchmarks suggest 20+ FPS achievable on GTX 5090 |
| **Setup Complexity** | ✅ Medium-High (70%) | Docker simplifies, but stitching adds complexity |
| **Community Support** | ✅ High (85%) | Active maintainers, responsive to 360° camera users |
| **Long-term Viability** | ✅ Medium-High (75%) | Active development, but IMU integration timeline unclear |
| **Overall Success** | ✅ Medium-High (75%) | Strong candidate with manageable risks |

### Go/No-Go Summary

**GO** - Stella VSLAM is a solid choice for this project with acceptable risks. The native equirectangular support, proven Insta360 compatibility, and active development make it the fastest path to a working prototype. The lack of native IMU integration is the primary drawback, but can be mitigated with external loose coupling.

**Estimated Success Probability**: 75%

**Key Success Factors**:
1. Efficient stitching pipeline implementation
2. Proper parameter tuning for Insta360 (following Discussion #158 guidance)
3. Acceptance of visual-only SLAM initially
4. Adequate testing in target indoor environments

---

## Appendix A: Quick Start Command Reference

### Docker Build

```bash
# Clone repository
git clone --recursive https://github.com/stella-cv/stella_vslam.git
cd stella_vslam

# Build CUDA-enabled Docker image (recommended)
docker build -t stella_vslam:cuda -f Dockerfile.cuda \
  --build-arg NUM_THREADS=$(nproc) .

# Run container with GPU support
docker run -it --rm --gpus all \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix/:/tmp/.X11-unix:ro \
  -v $(pwd)/data:/data \
  stella_vslam:cuda
```

### Download Vocabulary

```bash
# Inside container or local machine
wget https://github.com/stella-cv/FBoW_orb_vocab/raw/main/orb_vocab.fbow
```

### Run SLAM (Example)

```bash
# Inside Docker container at /stella_vslam_examples/build/

# SLAM with video file
./run_video_slam \
  -v /path/to/orb_vocab.fbow \
  -c /stella_vslam/example/aist/equirectangular.yaml \
  -m /path/to/video.mp4 \
  --frame-skip 1 \
  --no-sleep \
  --map-db map.msg
```

### Insta360 Configuration Template

```yaml
Camera:
  name: "Insta360 Stitched 4K"
  setup: "monocular"
  model: "equirectangular"
  fps: 30.0
  cols: 3840
  rows: 2160
  color_order: "RGB"

Feature:
  scale_factor: 1.2
  num_levels: 8
  max_num_keypoints: 5000
  ini_fast_threshold: 20
  min_fast_threshold: 7

System:
  map_format: "msgpack"
  num_grid_cols: 96
  num_grid_rows: 48
```

---

## Appendix B: Performance Optimization Checklist

- [ ] **Use Docker with CUDA** for GPU acceleration
- [ ] **Optimize stitching latency** (<50ms target)
- [ ] **Tune ORB parameters** for scene texture
- [ ] **Use 4K resolution** (not 5.7K) per community recommendations
- [ ] **Enable frame-skip** if needed (`--frame-skip 3` for 10 FPS from 30 FPS input)
- [ ] **Mask unnecessary regions** (top/bottom zenith/nadir if applicable)
- [ ] **Adjust max_num_keypoints** based on scene complexity
- [ ] **Monitor CPU/GPU usage** to identify bottlenecks
- [ ] **Profile tracking time** (target <0.05s for 20 FPS)
- [ ] **Test loop closure** in target environment
- [ ] **Implement graceful tracking recovery** after failure
- [ ] **Prune old keyframes** for long-duration flights

---

**Report End**

*For questions or clarifications, please refer to the GitHub Discussions at https://github.com/stella-cv/stella_vslam/discussions or contact the research team.*
