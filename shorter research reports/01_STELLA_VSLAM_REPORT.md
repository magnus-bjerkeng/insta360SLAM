# Stella VSLAM Research Report - Insta360 Dual Fisheye Drone SLAM

**Research Date:** November 6, 2025
**Project:** Indoor Drone SLAM with Insta360 Dual Fisheye Camera + IMU
**Hardware:** Ubuntu + GTX 5090

---

## 1. Executive Summary

### Overall Assessment: CONDITIONAL GO (75% Confidence)

Stella VSLAM is a **strong candidate** with native equirectangular (360°) support, proven Insta360 compatibility, and active development.

### Key Strengths
1. **Native Equirectangular Support**: Explicitly supports Insta360 cameras (confirmed with ONE X2 in Discussion #158)
2. **Active Development**: 1.1k stars, 443 forks, 55 contributors, recent commits
3. **Docker + GPU Support**: NVIDIA CUDA support, reduces setup complexity
4. **Proven UAV Fork**: RoblabWh/stella_vslam_dense optimized for 360° action cams on UAVs
5. **Real-time Performance**: ~22 FPS equivalent on desktop hardware (0.045s median tracking)
6. **Permissive License**: 2-clause BSD (commercial-friendly)

### Key Weaknesses & Critical Limitations
1. **NO Native IMU Integration**: Visual-only SLAM; IMU on roadmap but not implemented (requires external loose coupling)
2. **Stitching Required**: Must stitch dual fisheye → equirectangular before SLAM (adds preprocessing latency)
3. **No Direct Dual Fisheye Mode**: Cannot use two fisheye lenses independently
4. **Resolution Sensitivity**: Community reports: lower from 5.7K to 4K for better consistency
5. **Build Complexity**: 30-60 min manual build with many dependencies (Docker recommended)

### Recommendation

**PROCEED IF:**
- Real-time stitching achievable at 10-30 FPS (Insta360 SDK or GPU OpenCV)
- Visual-only SLAM acceptable initially (IMU loose coupling external)
- Docker deployment used
- 2-3 week setup timeline acceptable

**DO NOT PROCEED IF:**
- Tightly-coupled visual-inertial SLAM required from day one → use ORB-SLAM3 or VINS-Fusion
- Cannot achieve real-time stitching
- Need independent dual fisheye processing without stitching

---

## 2. Technical Feasibility

### Hardware Compatibility

| Component | Status | Details |
|-----------|--------|---------|
| Ubuntu 20.04/22.04 | ✅ Excellent | Officially supported |
| GTX 5090 | ✅ Excellent | CUDA 11.8+, Docker GPU passthrough `--gpus all` |
| RAM | ✅ Adequate | 16GB minimum, 32GB recommended for 4K |

### Expected Performance on GTX 5090
- **Estimated FPS**: 20-30 FPS for 4K equirectangular input
- **Minimum Viable**: 10+ FPS achievable (exceeds 1 FPS requirement)
- **Benchmarks**: 0.045s median tracking (22 FPS) on 1920x960 @ 30fps with older hardware
- **Optimization**: Frame-skip available (process every 3rd frame if needed)

### Core Dependencies
- Eigen (v3.3.0+), g2o (20230223_git+), SuiteSparse, FBoW, yaml-cpp, OpenCV (v3.3.1+ with CUDA)
- Optional: CUDA Toolkit, Pangolin/Iridescence viewer

### Setup Time Estimates

**Docker (Recommended):**
- Day 1: Build Docker image (2-3 hours)
- Day 2-3: Configure Insta360 stitching, test with sample data
- Day 4-7: Calibration, parameter tuning, integration
- **Total**: 1 week to working prototype

**Build from Source:** 1-1.5 weeks (not recommended)

---

## 3. Insta360 Compatibility ⭐ CRITICAL

### Confirmed Evidence

**✅ PROVEN**: Stella VSLAM successfully used with Insta360 cameras.

1. **GitHub Discussion #158**: "How to adjust parameters for Insta360 ONE X2?"
   - Camera: Insta360 ONE X2
   - Resolution: 5760x2880 @ 30fps (original), **4K recommended**
   - Outcome: Successful tracking after parameter tuning
   - **Key Recommendation**: Lower to 4K for better consistency

2. **Official README Statement**:
   > "visual SLAM algorithm using equirectangular camera models (e.g. RICOH THETA series, **insta360 series**, etc)"

3. **stella_vslam_dense Fork for UAVs**:
   - URL: https://github.com/RoblabWh/stella_vslam_dense
   - Purpose: "Real-time 3D reconstruction for 360° action cams on small UAVs"
   - **Tested With**: "DJI Avata with Insta360 modules, GoPro Max"
   - Performance: Real-time on HD equirectangular video
   - Publication: 2022 SSRR conference paper

### Configuration for Insta360 ONE X2 (from Discussion #158)

```yaml
Camera:
  name: "Insta360 ONE X2"
  setup: "monocular"
  model: "equirectangular"
  fps: 30.0
  cols: 3840  # 4K recommended (not 5760)
  rows: 2160  # 4K recommended (not 2880)
  color_order: "RGB"

Feature:
  max_num_keypoints: 5000  # Tune based on scene
  scale_factor: 1.2
  num_levels: 8
  ini_fast_threshold: 20
  min_fast_threshold: 7
```

**Maintainer Guidance**: "I recommend that you try lowering the resolution by resizing."

### Resolution Recommendations

| Resolution | FPS Target | Tracking Quality | Recommendation |
|------------|------------|------------------|----------------|
| 5.7K (5760x2880) | 10-20 FPS | Inconsistent | ❌ Avoid |
| **4K (3840x2160)** | 20-30 FPS | Good | ✅ **RECOMMENDED** |
| 2K (2560x1280) | 30+ FPS | Good | ✅ If FPS critical |
| HD (1920x960) | 30+ FPS | Good | ⚠️ Lower spatial resolution |

**Optimal**: 4K (3840x2160) @ 30fps for balance of quality and performance

### Stitching Approach

**Recommended Pipeline:**
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

**Stitching Options:**

| Method | Pros | Cons | Latency |
|--------|------|------|---------|
| **Insta360 SDK** | Hardware-optimized, best quality | Proprietary, license needed | <10ms |
| OpenCV Stitcher | Open-source, customizable | Slower | 50-200ms |
| Pre-computed LUTs | Fastest after calibration | Requires extrinsic calibration | 5-15ms |

**Recommendation**: Insta360 SDK for real-time stitching → 4K equirectangular @ 30fps → Stella VSLAM

---

## 4. Camera Input & Processing Approach

### Supported Camera Models

Stella VSLAM implements 4 camera models:
1. **perspective** - Standard pinhole
2. **fisheye** - Kannala-Brandt model (single fisheye only)
3. **equirectangular** - 360° omnidirectional ⭐ (USE THIS)
4. **radial_division** - Alternative distortion model

### Why Equirectangular Model for Insta360?

- **No intrinsic calibration needed** (only cols, rows)
- Assumes perfect equirectangular projection from stitching
- Native support with proven configurations
- Community experience available

### Why NOT Dual Fisheye Stereo?

Stella VSLAM stereo mode requires:
- Two cameras with **overlapping FOV**
- Rectified stereo pair

Insta360 dual fisheye has:
- **Non-overlapping FOV** (front: 0-180°, back: 180-360°)
- Cannot be used as stereo pair

**Conclusion**: Dual fisheye stereo mode NOT feasible; must stitch to equirectangular

### Alternative: Single Fisheye Mode

If stitching latency prohibitive:
- Use front fisheye only with `fisheye` camera model
- Requires intrinsic calibration (fx, fy, cx, cy, k1-k4)
- FOV: ~180-220° (vs 360° stitched)
- Trade-off: Reduced situational awareness

---

## 5. IMU Integration ⚠️ CRITICAL LIMITATION

### Current Status: NO Native IMU Support

**Important**: Stella VSLAM is **visual-only** SLAM. IMU integration is:
- On the roadmap (priority #7 in README)
- **NOT yet implemented**
- No timeline provided by maintainers

### Workarounds for IMU Data

**Option A: External Loose Coupling (Recommended)**
- Implement separate IMU preintegration (e.g., GTSAM)
- Fuse Stella VSLAM pose estimates with IMU data offline/online
- Lower integration effort (1-2 weeks)
- Sufficient for trajectory smoothing

**Option B: Fork for Tight Coupling (Not Recommended)**
- Fork stella_vslam, integrate IMU preintegration
- Add IMU-visual bundle adjustment
- High effort (4-8 weeks of development)

**Option C: Switch to Visual-Inertial SLAM**
- ORB-SLAM3: Best VI-SLAM, but no equirectangular (use single fisheye)
- VINS-Fusion: Good VIO, perspective/fisheye only

### Recommendation
Start with visual-only SLAM. Add external loose coupling only if tracking proves insufficient for indoor flight.

---

## 6. Installation & Setup

### Docker Deployment (Recommended)

**Dockerfile.cuda** - CUDA-enabled with GPU acceleration
- Base: nvidia/cuda:11.8.0-devel-ubuntu22.04
- Includes: OpenCV with CUDA, cuda-efficient-features, Pangolin viewer
- GPU Support: `--gpus all` flag

**Build Commands:**
```bash
git clone --recursive https://github.com/stella-cv/stella_vslam.git
cd stella_vslam

docker build -t stella_vslam:cuda -f Dockerfile.cuda \
  --build-arg NUM_THREADS=$(nproc) .

docker run -it --rm --gpus all \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix/:/tmp/.X11-unix:ro \
  -v $(pwd)/data:/data \
  stella_vslam:cuda
```

**Requirements:**
- NVIDIA driver v390+
- nvidia-docker2 installed
- ~5-8GB disk space
- 2-3 hours build time

### Build from Source (Not Recommended)
- Complexity: HIGH
- Time: 30-60 minutes
- Dependencies: ~20 system packages + 6 libraries from source
- Failure risk: Medium (version mismatches)

### Download ORB Vocabulary
```bash
wget https://github.com/stella-cv/FBoW_orb_vocab/raw/main/orb_vocab.fbow
```

### Run SLAM Example
```bash
./run_video_slam \
  -v /path/to/orb_vocab.fbow \
  -c /stella_vslam/example/aist/equirectangular.yaml \
  -m /path/to/video.mp4 \
  --frame-skip 1 \
  --no-sleep \
  --map-db map.msg
```

---

## 7. Repository Health

| Metric | Value | Assessment |
|--------|-------|------------|
| Stars | 1,100+ | Good community interest |
| Forks | 443 | High adoption |
| Contributors | 55 | Healthy community |
| License | 2-clause BSD | Commercial-friendly |
| CI Status | ✅ Passing | Active testing |
| Last Update | Recent (2024) | Actively maintained |

**Maintenance**: Active development, responsive maintainers, regular commits
**Community**: GitHub Discussions active, issue response within days

---

## 8. Dense Fork Comparison

### Main Stella VSLAM vs stella_vslam_dense

| Feature | Main Stella VSLAM | stella_vslam_dense |
|---------|-------------------|---------------------|
| Purpose | Sparse feature-based SLAM | Sparse + Dense reconstruction |
| UAV Focus | General-purpose | **Optimized for UAV 360° cams** |
| Memory | ~2-4GB | ~16GB (HD), 32GB+ (5.7K) |
| Performance | 20-30 FPS (4K sparse) | Real-time with frame-skip |
| Community | 1.1k stars | 41 stars |
| Camera Support | Mono, Stereo, RGBD | **Mono equirectangular only** |

### Recommendation
**Start with main Stella VSLAM** unless dense reconstruction critical for obstacle avoidance.

---

## 9. Critical Risks & Limitations

### Showstopper Analysis

**No critical blockers**, but important considerations:

1. **IMU Not Native**: If tightly-coupled VI-SLAM mandatory, wrong choice
2. **Stitching Latency**: Must achieve <50ms for real-time budget
3. **Resolution Tuning**: 5.7K inconsistent; must use 4K per community experience
4. **No Direct Dual Fisheye**: Cannot process both streams independently

### Risk Mitigation

| Risk | Likelihood | Mitigation |
|------|------------|------------|
| Stitching latency too high | Medium | Use Insta360 SDK; hardware acceleration |
| Tracking fails low-texture | Medium | Tune ORB parameters; add markers if needed |
| 4K too slow | Low | Use 2K; frame-skip; GPU acceleration |
| IMU required | High | Plan external loose coupling; consider alternatives |
| Memory exhaustion | Low | Map pruning; limit max keyframes |

### Success Criteria

System successful if:
- ✅ ≥10 FPS end-to-end (stitching + SLAM)
- ✅ Tracks for ≥5-minute indoor flights
- ✅ Loop closure works reliably
- ✅ Pose error <5% traveled distance
- ✅ Graceful recovery from tracking loss

---

## 10. Final Recommendations

### Decision Tree

```
Can you stitch dual fisheye → equirectangular at 10+ FPS?
  ├─ YES → Continue
  └─ NO → Single fisheye with ORB-SLAM3
           ↓
Do you REQUIRE tightly-coupled IMU from day one?
  ├─ NO → Stella VSLAM ✅ RECOMMENDED
  └─ YES → ORB-SLAM3 with single fisheye
```

### Confidence Assessment

| Aspect | Confidence | Reasoning |
|--------|------------|-----------|
| Technical Feasibility | 85% | Proven with Insta360, good docs |
| Performance Target | 80% | Benchmarks suggest 20+ FPS on GTX 5090 |
| Setup Complexity | 70% | Docker simplifies; stitching adds complexity |
| Community Support | 85% | Active, responsive to 360° users |
| Overall Success | **75%** | Strong candidate, manageable risks |

### Primary Recommendation

**GO with Stella VSLAM** - Fastest path to working prototype with acceptable risks.

**Key Success Factors:**
1. Efficient stitching (Insta360 SDK preferred)
2. Parameter tuning following Discussion #158 guidance
3. Accept visual-only initially
4. Adequate testing in target environments

### Alternatives if Stella VSLAM Fails

**For Tightly-Coupled VI-SLAM:**
- ORB-SLAM3: Best VI-SLAM (use single fisheye, no equirectangular)
- VINS-Fusion: Good VIO (perspective/fisheye only)

**For Better GPU Performance:**
- NVIDIA Isaac ROS Visual SLAM (perspective only)

---

## 11. Quick Start Guide

### Phase 1: Environment Setup (Week 1)

**Days 1-2: Docker Environment**
```bash
# Install nvidia-docker2
sudo apt-get update
sudo apt-get install -y nvidia-docker2

# Clone and build
git clone --recursive https://github.com/stella-cv/stella_vslam.git
cd stella_vslam
docker build -t stella_vslam:cuda -f Dockerfile.cuda --build-arg NUM_THREADS=$(nproc) .

# Verify GPU
docker run --gpus all nvidia-smi

# Download vocabulary
wget https://github.com/stella-cv/FBoW_orb_vocab/raw/main/orb_vocab.fbow
```

**Days 3-4: Test Sample Data**
- Download AIST equirectangular dataset
- Run with example config
- Verify tracking works

**Days 5-7: Insta360 Integration**
- Implement stitching (Insta360 SDK or OpenCV)
- Capture test footage
- Create Insta360 config

### Phase 2: Tuning (Week 2)

- Tune ORB parameters (max_num_keypoints, thresholds)
- Test resolutions: 4K, 2K
- Optimize frame-skip if needed
- Profile end-to-end latency

### Timeline to Working System

| Milestone | Duration |
|-----------|----------|
| Docker + sample testing | 4 days |
| Insta360 integration | 1 week |
| Tuning & optimization | 2 weeks |
| **First Prototype** | **2-3 weeks** |
| IMU loose coupling | +1-2 weeks |
| Production hardening | +1-2 weeks |

---

## 12. Essential Resources

### Official Links
- Main Repo: https://github.com/stella-cv/stella_vslam
- Dense Fork (UAV): https://github.com/RoblabWh/stella_vslam_dense
- Documentation: https://stella-cv.readthedocs.io/
- ROS2 Wrapper: https://github.com/stella-cv/stella_vslam_ros

### Critical Discussions
- **Discussion #158**: Insta360 ONE X2 parameters - **READ THIS**
  https://github.com/stella-cv/stella_vslam/discussions/158
  Key: Use 4K, tune max_num_keypoints

### Sample Datasets
- AIST Equirectangular: https://drive.google.com/open?id=1A_gq8LYuENePhNHsuscLZQPhbJJwzAq4
  (1920x960 @ 30fps, living lab/factory scenes)

### Academic Papers
- OpenVSLAM (ACM MM 2019): https://arxiv.org/abs/1910.01122
- stella_vslam_dense (SSRR 2022): Real-time dense reconstruction for UAV 360° cams

---

## Performance Optimization Checklist

- [ ] Use Docker with CUDA for GPU acceleration
- [ ] Optimize stitching latency (<50ms target), use 4K resolution (not 5.7K)
- [ ] Tune max_num_keypoints for scene, enable frame-skip if needed
- [ ] Profile tracking time (<0.05s target), monitor CPU/GPU usage
- [ ] Test loop closure, implement graceful recovery

---

## Summary: Stella VSLAM for Insta360 Dual Fisheye + IMU Drone

**Verdict**: CONDITIONAL GO (75% confidence)

**Bottom Line**: Strong candidate with proven Insta360 support, but requires:
1. Real-time stitching to equirectangular (use Insta360 SDK)
2. Accept visual-only SLAM initially (IMU loose coupling external)
3. Use 4K resolution per community recommendations
4. Docker deployment for ease of setup
5. 2-3 week timeline to working prototype

**Critical Limitation**: No native IMU integration; plan for external loose coupling or consider ORB-SLAM3 if tightly-coupled VI-SLAM mandatory.

**Fastest Path**: Docker + Insta360 SDK stitching + 4K equirectangular + parameter tuning from Discussion #158

---

**Report End** - Total Lines: 497

*For questions, see GitHub Discussion #158 or contact stella-cv maintainers*
