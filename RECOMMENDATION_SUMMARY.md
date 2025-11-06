# SLAM Method Recommendation for Insta360 Dual Fisheye Drone

**Date**: 2025-11-06
**Analysis**: Comprehensive review of 7 SLAM research reports
**Decision**: VINS-Fusion with dual non-overlapping fisheye extension

---

## Executive Recommendation

### ✅ SELECTED METHOD: VINS-Fusion

**Repository**: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion
**Confidence**: 85% (single fisheye) / 70% (dual fisheye extension)
**Timeline**: 6-8 weeks to production

---

## Why VINS-Fusion?

### 1. **Drone-Proven Heritage**
- Developed by HKUST Aerial Robotics specifically for UAVs
- Extensively tested on real drone flights
- Omni-swarm project: Dual fisheye on aerial swarms, centimeter accuracy
- DFOM project: Dual fisheye omnidirectional mapping on drones

### 2. **Strong IMU Integration**
- Tightly-coupled visual-inertial SLAM (essential for drones)
- Handles aggressive maneuvers and fast motions
- Indoor-optimized with robust initialization

### 3. **Proven Fisheye Support**
- Native Kannala-Brandt model for ~200° FOV
- Well-documented, community-tested
- Works directly with raw fisheye (no undistortion)

### 4. **Dual Fisheye Feasibility**
- Research papers prove dual fisheye works (Omni-swarm, DFOM)
- Extension approach: Dual monocular instances + trajectory fusion
- Timeline: 1-2 weeks for basic dual, 3-4 weeks for optimal fusion

### 5. **Excellent Performance**
- 25-30 FPS expected (exceeds 1-30 FPS requirement)
- 2-5 cm trajectory accuracy with IMU
- <1% drift over 100m with loop closure

### 6. **Large Community**
- 4,200+ GitHub stars (largest among candidates)
- Extensive documentation and tutorials
- Active community support

---

## Comparison with Alternatives

| System | Dual Fisheye | IMU | Performance | Setup | Confidence | Notes |
|--------|--------------|-----|-------------|-------|------------|-------|
| **VINS-Fusion** ⭐ | ⚠️ Extension | ✅ Excellent | 25-30 FPS | 6-8 wks | **85%/70%** | **RECOMMENDED** |
| ORB-SLAM3 | ❌ Single only | ✅ Best (9mm) | 30-40 FPS | 2-3 wks | 85% | Excellent fallback |
| VINS-OS + Driver | ✅ Native | ✅ Good | 20-30 FPS | 3-4 wks | 75% | Alternative |
| OpenVINS | ⚠️ Experimental | ✅ Filter | 20-30 FPS | 4-8 wks | 75% | Filter-based |
| Basalt | ⚠️ Uncertain | ✅ Excellent | 15-30 FPS | 2-3 wks | 70% | Multi-cam uncertain |
| Stella VSLAM | ❌ Stitching | ❌ **No IMU** | 20-30 FPS | 2-3 wks | 75% | Visual-only risky |
| Kimera | ❌ Unavailable | ✅ Good | 10-20 FPS | 3-6 mo | **5%** | Code not released |

---

## Implementation Strategy

### Incremental Validation Approach

```
Phase 1 (Weeks 1-3): Single Fisheye Validation
    ↓
    Deploy VINS-Fusion with front OR back camera + IMU
    Validate: 15+ Hz, reliable tracking, <5% drift
    SUCCESS RATE: 85%
    ↓
Phase 2 (Weeks 4-6): Dual Fisheye Extension
    ↓
    Run two VINS instances (one per camera)
    Create trajectory fusion node
    Optimize performance
    SUCCESS RATE: 70%
    ↓
Phase 3 (Weeks 7-8): Production Hardening
    ↓
    Stress testing, drone integration, failsafes
    Flight testing and validation
```

### Why This Approach?

1. **De-risks development**: Validate single camera works before dual complexity
2. **Fast initial results**: Working system in 3 weeks (single camera)
3. **Decision points**: Can stop at single camera if sufficient (85% chance it is)
4. **Research-backed**: Omni-swarm/DFOM papers prove dual fisheye feasible
5. **Incremental investment**: Don't commit to dual until single validated

---

## Key Technical Decisions

### 1. Camera Configuration
- **Hardware**: Insta360 One X3 (~$450) or X2 (~$300)
- **Specs**: Dual 195° fisheye, 6-axis IMU @ 500Hz, 4K@30fps
- **Driver**: ai4ce/insta360_ros_driver (ROS2) or custom (ROS1)

### 2. Platform
- **OS**: Ubuntu 20.04
- **Framework**: ROS Noetic
- **SLAM**: VINS-Fusion (Kannala-Brandt fisheye model)

### 3. Calibration (CRITICAL)
- **Tool**: Kalibr (industry standard)
- **Requirements**:
  - Camera intrinsics: <0.5 pixel reprojection error
  - Camera-IMU extrinsics: Careful multi-DOF excitation
  - IMU characterization: 2h stationary data OR datasheet × 10
- **Timeline**: 2-3 days (do not rush this!)

### 4. Dual Fisheye Approach
- **Method**: Dual monocular VINS instances + trajectory fusion
- **Fusion**: Start with weighted averaging, upgrade to EKF if needed
- **Baseline**: Omni-swarm paper (HKUST, IEEE T-RO 2021)

---

## Expected Performance

### Single Fisheye (Phase 1)
- **FPS**: 25-30 Hz
- **Accuracy**: 2-5 cm trajectory error
- **Coverage**: ~200° FOV (front OR back hemisphere)
- **Drift**: <2% over 100m
- **Initialization**: 2-3 seconds

### Dual Fisheye (Phase 2)
- **FPS**: 15-25 Hz (dual processing)
- **Accuracy**: 1-3 cm (improved from redundancy)
- **Coverage**: ~360° omnidirectional
- **Drift**: <1% over 100m
- **Robustness**: Single camera occlusion handled

---

## Risk Assessment

### High Confidence (85%+)
- ✅ Single fisheye + IMU will work
- ✅ Calibration process well-documented
- ✅ VINS-Fusion stable and mature
- ✅ Performance will exceed 1 FPS minimum

### Medium Confidence (70%)
- ⚠️ Dual fisheye fusion quality
- ⚠️ Non-overlapping camera coordination
- ⚠️ Real-time performance with dual processing

### Mitigations
1. **Start with single camera** (proven path, de-risks)
2. **Follow research references** (Omni-swarm, DFOM papers)
3. **Excellent calibration** (most critical success factor)
4. **Incremental validation** (test at each phase)
5. **Fallback options** (ORB-SLAM3 single fisheye if needed)

---

## Critical Success Factors

### 1. Calibration Quality (MOST IMPORTANT)
- Use Kalibr (not OpenCV alone)
- AprilTag board: 6x6, 0.8m size, rigid mounting
- Reprojection error: <0.5 pixels target
- Camera-IMU extrinsics: Multi-DOF excitation sequence
- IMU characterization: Use imu_utils (2h stationary data)

### 2. Proper IMU Integration
- Verify 200-500 Hz IMU rate
- Accurate noise parameters (characterize or datasheet × 10)
- Time synchronization (hardware preferred, software acceptable)
- Sufficient motion during initialization (translation + rotation)

### 3. Incremental Validation
- Don't skip single camera phase
- Validate each component before proceeding
- Test in target environment early
- Collect performance metrics continuously

### 4. Development Environment
- Ubuntu 20.04 (not 22.04 for ROS Noetic)
- Ceres 1.14.0 (NOT 2.x - critical version requirement)
- Proper catkin workspace setup
- Good C++ debugging tools

### 5. Community Engagement
- Post GitHub issues early if stuck
- Reference Omni-swarm/DFOM papers
- Join ROS Discourse for VINS questions
- Document your setup for others

---

## Alternative Paths

### If Single Fisheye Sufficient
- **Deploy**: Use ORB-SLAM3 for best accuracy (9mm vs 2-5cm)
- **Timeline**: Save 3-4 weeks
- **Trade-off**: Limited FOV (~200°) vs full 360°

### If Dual Monocular Fusion Fails
- **Plan B**: VINS-OS with native dual fisheye stereo
- **Plan C**: Basalt (better multi-camera architecture)
- **Plan D**: ORB-SLAM3 single camera (fallback)

### If VINS-Fusion Unsuitable
Ranked alternatives:
1. **VINS-OS + Insta360 driver** (75% confidence, 3-4 weeks)
2. **OpenVINS dual extension** (75% confidence, 4-6 weeks)
3. **ORB-SLAM3 single** (85% confidence, 2-3 weeks)
4. **Basalt** (70% confidence, 2-3 weeks)

---

## Resources Provided

### 1. Implementation Plan
**File**: `VINS_FUSION_DUAL_FISHEYE_IMPLEMENTATION_PLAN.md`
- Complete 6-8 week roadmap
- Day-by-day task breakdown
- All commands and configurations
- Troubleshooting guide
- Validation criteria

### 2. Research Reports (Original)
**Folder**: `shorter research reports/`
- 01: Stella VSLAM (visual-only, equirectangular)
- 02: Basalt (multi-camera uncertain)
- 03: VINS-Fusion (RECOMMENDED)
- 04: Kimera (code unavailable)
- 05: ORB-SLAM3 (excellent single camera)
- 06: OpenVINS (filter-based)
- 07: Insta360-specific (driver info)

---

## Next Steps

### Immediate Actions (This Week)

1. **Apply for Insta360 SDK**
   - URL: https://www.insta360.com/sdk/apply
   - Approval: 3-7 days
   - Required for official driver

2. **Order Hardware**
   - Insta360 One X3 ($450) or X2 ($300-350)
   - AprilTag calibration board materials

3. **Prepare Environment**
   - Install Ubuntu 20.04 (if not already)
   - Allocate 50GB disk space for development
   - Ensure GTX 5090 drivers installed

### Week 1 Start
Follow implementation plan:
- Day 1-2: Install ROS Noetic
- Day 3-4: Install VINS-Fusion
- Day 5-7: Install Insta360 SDK & driver

---

## Success Metrics

### Minimum Viable Product (Week 3)
- ✅ 15+ FPS
- ✅ Reliable initialization (<5 sec)
- ✅ 80%+ tracking success
- ✅ <5% drift over 100m

### Production System (Week 8)
- ✅ 15-25 FPS (dual camera)
- ✅ 360° coverage
- ✅ <2% drift over 100m
- ✅ Robust to occlusion
- ✅ 10+ minute stable flights

---

## Conclusion

**VINS-Fusion is the optimal choice** for Insta360 dual fisheye drone SLAM based on:

1. ✅ Proven drone deployment record
2. ✅ Research validation (Omni-swarm, DFOM)
3. ✅ Strong IMU integration (critical for drones)
4. ✅ Feasible dual fisheye extension (70% confidence)
5. ✅ Excellent performance (25-30 FPS)
6. ✅ Large community (4,200+ stars)
7. ✅ Incremental validation path (de-risks)

**Timeline**: 6-8 weeks to production dual-fisheye SLAM

**Confidence**: 85% single camera, 70% dual camera, 90% overall (with fallback)

**Recommendation**: Proceed with VINS-Fusion implementation plan immediately.

---

## Contact & Support

**Questions during implementation?**
- VINS-Fusion issues: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion/issues
- Kalibr questions: https://github.com/ethz-asl/kalibr/wiki
- ROS Discourse: https://discourse.ros.org/
- Reference papers: Omni-swarm (arXiv:2103.04131), DFOM

**Key researchers** (for advanced questions):
- Tong Qin: VINS-Fusion lead developer
- Wenliang Gao: DFOM/VINS-OS dual fisheye expert
- Shaojie Shen: HKUST Aerial Robotics lab director

---

**Document Version**: 1.0
**Last Updated**: 2025-11-06
**Status**: Ready for implementation
