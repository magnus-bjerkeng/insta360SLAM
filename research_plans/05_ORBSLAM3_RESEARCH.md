# Research Plan: ORB-SLAM3 for Insta360 Dual Fisheye Drone

## Mission Context

**Project**: Indoor drone SLAM with Insta360 dual fisheye camera + IMU
**Hardware**: GTX 5090, Ubuntu
**Camera**: Insta360 with two non-overlapping fisheye lenses (front & back)
**Sensors**: IMU available, extrinsic calibration known
**Requirements**: Minimum 1 FPS, preferably real-time (10-30 FPS)
**Approach for this system**: Visual-inertial SLAM with Kannala-Brandt fisheye model

---

## Why ORB-SLAM3?

ORB-SLAM3 is the **⭐ STATE-OF-THE-ART FEATURE-BASED SLAM**:
- **Industry standard** - most cited visual SLAM system
- **Native fisheye support** via Kannala-Brandt camera model
- **Visual-Inertial mode** - tightly-coupled IMU integration
- **Multiple sensor configurations**: Monocular, stereo, RGB-D, monocular-inertial, stereo-inertial
- **Loop closure** - robust place recognition
- **Map reuse** - multi-session SLAM capability
- **Tested on TUM VI and EuRoC** - fisheye and IMU datasets
- **High accuracy** - "as robust as the best in literature, significantly more accurate"

**Timeline estimate**: 2-3 weeks (single fisheye + IMU)

---

## Research Objectives

Conduct a **deep technical investigation** of ORB-SLAM3 to determine suitability for our Insta360 + IMU drone project:
1. Kannala-Brandt fisheye model compatibility with Insta360
2. Visual-inertial (VI) mode performance and configuration
3. Single vs dual fisheye options
4. Modification requirements for dual non-overlapping cameras
5. State-of-the-art accuracy vs complexity trade-off

---

## Critical Questions to Answer

### 1. Can We Run This on Our Machine?
- [ ] **OS Compatibility**: Ubuntu versions supported?
- [ ] **GPU Requirements**: Can GTX 5090 accelerate ORB feature extraction?
- [ ] **CPU Requirements**: Multi-threading? Core recommendations?
- [ ] **Dependencies**:
  - Pangolin (visualization)
  - OpenCV version requirements
  - Eigen3
  - DBoW2 and g2o (bundled?)
- [ ] **Build System**: CMake complexity?
- [ ] **Performance metrics**: FPS on TUM VI (fisheye dataset)?
- [ ] **Memory requirements**: RAM usage during operation?
- [ ] **ROS support**: Is there ROS wrapper available?

### 2. Repository Health & Community
- [ ] **GitHub**: https://github.com/UZ-SLAMLab/ORB_SLAM3
- [ ] **Stars/Forks**: How popular? (Likely very high)
- [ ] **Last commit**: Active maintenance?
- [ ] **Contributors**: UZ SLAMLab (University of Zaragoza) + community
- [ ] **Releases**: Stable releases? Version 1.0?
- [ ] **License**: GPLv3 (check if this is acceptable)
- [ ] **Academic backing**: Active research group?
- [ ] **Citation count**: Original ORB-SLAM + ORB-SLAM2 + ORB-SLAM3 papers

### 3. Open Issues Relevant to Our Use Case
Search for:
- [ ] "fisheye" - fisheye camera issues
- [ ] "Kannala-Brandt" - camera model issues
- [ ] "insta360" - Insta360 mentions
- [ ] "multi-camera" or "dual camera" - multi-camera support
- [ ] "non-overlapping" - non-overlapping camera discussions
- [ ] "drone" or "UAV" - drone applications
- [ ] "inertial" or "IMU" - VI mode issues
- [ ] "TUM VI" - issues with fisheye dataset
- [ ] "calibration" - calibration problems
- [ ] "performance" - performance issues

### 4. Documentation Quality
- [ ] **README**: Comprehensive?
- [ ] **Documentation folder**: Detailed docs?
- [ ] **Installation guide**: Step-by-step?
- [ ] **Camera models**: Kannala-Brandt configuration explained?
- [ ] **Config files**: Example configs for fisheye + IMU?
- [ ] **Calibration guide**: How to calibrate fisheye cameras?
- [ ] **IMU parameters**: How to configure IMU?
- [ ] **Academic paper**: Good learning resource?
- [ ] **Community tutorials**: Blog posts, YouTube videos?

**Rating**: Excellent / Good / Fair / Poor

### 5. Setup Complexity
- [ ] **Build process**: How many steps?
- [ ] **Dependencies**: Easy to install?
- [ ] **Vocabulary file**: ORB vocabulary download size and time?
- [ ] **Calibration**: Tools and process for Kannala-Brandt?
- [ ] **Configuration**: YAML file complexity?
- [ ] **IMU configuration**: Noise parameters, extrinsics, etc.?
- [ ] **Data input**: What formats? (Video, image sequence, ROS bag?)

**Estimate**: Simple / Moderate / Complex / Very complex

### 6. Dockerized Version
- [ ] **Official Docker**: Dockerfile in repo?
- [ ] **Docker Hub**: Pre-built images?
- [ ] **Community Docker images**: Third-party containers?
- [ ] **GPU support**: CUDA in Docker?
- [ ] **ROS Docker**: ROS + ORB-SLAM3 images?

### 7. Insta360-Specific Implementations
- [ ] **ORB-SLAM3 + Insta360**: GitHub search for projects
- [ ] **ORB-SLAM3 + fisheye**: Fisheye camera examples
- [ ] **ORB-SLAM3 + 360 camera**: 360° camera usage
- [ ] **Research papers**: Using ORB-SLAM3 with wide-FOV cameras
- [ ] **Community projects**: Drone applications
- [ ] **Fisheye calibration**: Example calibration files

**Key search**: "ORB-SLAM3 fisheye", "ORB-SLAM3 Kannala-Brandt", "ORB-SLAM3 TUM VI"

### 8. Camera Input Options
- [ ] **Kannala-Brandt model**: Confirmed for fisheye?
- [ ] **FOV range**: What FOV does Kannala-Brandt support well?
- [ ] **Monocular-Inertial**: Single fisheye + IMU supported?
- [ ] **Stereo-Inertial**: Dual fisheye (with overlap) + IMU?
- [ ] **Dual non-overlapping**: Possible with modifications?
- [ ] **Stitched input**: Can we feed equirectangular?

**Determine options**:
- Option A: Single fisheye + IMU (monocular-inertial mode) → standard
- Option B: Dual fisheye as stereo + IMU → requires overlap
- Option C: Dual non-overlapping + IMU → requires modification
- Option D: Stitched omnidirectional → treat as monocular

---

## Investigation Steps

### Step 1: Repository Deep Dive (45 minutes)
1. Visit https://github.com/UZ-SLAMLab/ORB_SLAM3
2. Read README thoroughly
3. Check Documentation/ folder if exists
4. Examine Examples/ directory
5. Look at CMakeLists.txt for dependencies
6. Check GitHub insights (stars, forks, issues, PRs)
7. Find latest release and check changelog

### Step 2: Academic Paper Review (60 minutes)
1. Find ORB-SLAM3 paper:
   - "ORB-SLAM3: An Accurate Open-Source Library for Visual, Visual-Inertial and Multi-Map SLAM"
   - Campos et al. 2020
2. Read abstract and fisheye-related sections
3. Check TUM VI dataset results (fisheye + IMU)
4. Look for performance metrics and accuracy
5. Check Google Scholar citation count
6. Look for papers using ORB-SLAM3 with fisheye

### Step 3: Kannala-Brandt Model Analysis (30 minutes)
1. Understand the camera model:
   - What FOV does it support?
   - How many parameters?
   - Calibration requirements?
2. Find example config files with Kannala-Brandt
3. Look for calibration examples (OpenCV, Kalibr?)
4. Determine if suitable for Insta360 lenses

### Step 4: Visual-Inertial Mode Investigation (45 minutes)
1. Find VI mode documentation
2. Check IMU configuration requirements:
   - IMU intrinsics (noise, bias, random walk)
   - Camera-IMU extrinsics
   - Time synchronization requirements
3. Look for example config files for VI mode
4. Find performance comparisons: visual-only vs VI
5. Check initialization requirements for VI mode

### Step 5: Issue Analysis (45 minutes)
1. Search GitHub issues with keywords
2. Filter for fisheye-related issues
3. Check VI mode issues
4. Look for calibration problems
5. Check for drone/UAV discussions
6. Assess maintainer responsiveness
7. Note any critical bugs

### Step 6: TUM VI Dataset Analysis (30 minutes)
1. ORB-SLAM3 was tested on TUM VI (fisheye + IMU)
2. Find results and configuration used
3. Check if example config files are provided
4. Look for community reproductions of TUM VI results
5. Understand how well it performs on fisheye data

### Step 7: Community Search (60 minutes)
1. **GitHub search**:
   - "ORB-SLAM3 fisheye"
   - "ORB-SLAM3 insta360"
   - "ORB-SLAM3 drone"
2. **YouTube**: ORB-SLAM3 tutorials and demos
3. **Google Scholar**: Papers citing ORB-SLAM3
4. **Reddit**: r/computervision, r/robotics
5. **ROS Discourse**: ORB-SLAM3 discussions
6. **Personal blogs**: Setup guides and tips

### Step 8: Multi-Camera Feasibility (45 minutes)
1. Check if ORB-SLAM3 supports multi-camera natively
2. Search for multi-camera extensions or forks
3. Look for dual non-overlapping camera research
4. Assess modification requirements for our dual fisheye
5. Check if anyone has attempted this

### Step 9: Docker & Deployment (20 minutes)
1. Check for Dockerfile in repo
2. Search Docker Hub
3. Look for community Docker images
4. Check for ROS wrappers and Docker images

### Step 10: ROS Integration (20 minutes)
1. Check if official ROS wrapper exists
2. Search for "ORB-SLAM3 ROS" community wrappers
3. Assess ROS integration quality
4. Check ROS distribution compatibility

---

## Deliverables

Provide a **detailed report** with these sections:

### 1. Executive Summary (2-3 paragraphs)
- Is ORB-SLAM3 suitable for Insta360 drone?
- Key strengths and limitations
- Recommendation: Go/No-Go with confidence

### 2. Fisheye Support Analysis (1-1.5 pages) ⭐ CRITICAL
- Kannala-Brandt model capabilities
- FOV range suitable for Insta360?
- Configuration and calibration
- TUM VI dataset performance
- Example configs available?

### 3. Visual-Inertial Mode (1 page) ⭐ CRITICAL
- VI mode configuration requirements
- IMU parameter setup
- Performance gains from IMU
- Initialization process
- Tightly-coupled vs loosely-coupled

### 4. Single vs Dual Fisheye (1 page) ⭐ CRITICAL
- Option A: Single fisheye + IMU (monocular-inertial)
  - Officially supported?
  - Expected performance?
- Option B: Dual non-overlapping modification
  - Feasibility?
  - Required modifications?
  - Community examples?
- Recommended approach

### 5. Technical Feasibility (1 page)
- Ubuntu + hardware compatibility
- Dependencies and build process
- Expected performance (FPS)
- Memory requirements
- GPU acceleration possibilities

### 6. Repository Health (0.5 page)
- Stars, forks, activity
- UZ SLAMLab backing
- Maintenance status
- Community size (likely large)

### 7. Critical Issues (0.5-1 page)
- Relevant issues for fisheye + VI mode
- Showstoppers?
- Community and maintainer responsiveness

### 8. Documentation Assessment (0.5 page)
- Quality rating
- Installation and setup docs
- Fisheye configuration docs
- VI mode documentation

### 9. Setup Complexity (0.5-1 page)
- Installation process
- Vocabulary file download
- Calibration requirements
- Configuration complexity
- Time estimate

### 10. Deployment Options (0.5 page)
- Docker availability
- ROS integration options
- Build from source

### 11. Insta360/Fisheye Evidence (1 page) ⭐ CRITICAL
- Projects using ORB-SLAM3 with fisheye
- Kannala-Brandt calibration examples
- Community success stories
- Confidence for our setup

### 12. Performance Expectations (0.5 page)
- Expected FPS on our hardware
- Accuracy expectations (based on TUM VI)
- CPU/GPU utilization
- Comparison with ORB-SLAM2

### 13. Comparison with Alternatives (0.5 page)
- vs Stella VSLAM (simpler)
- vs Basalt (multi-camera native)
- vs VINS-Fusion (drone-proven)
- Why choose ORB-SLAM3?

### 14. Next Steps (0.5-1 page)
If we proceed:
- Deployment roadmap
- Calibration process
- Configuration steps
- Timeline estimate
- Risk mitigation

### 15. Resource Links
- Repository
- Papers
- Example projects
- Calibration tools
- Community resources

---

## Success Criteria

✅ Can answer:
1. Does Kannala-Brandt model work well for Insta360 fisheye?
2. Can we run monocular-inertial (single fisheye + IMU)?
3. Is VI mode well-supported and documented?
4. Can we run on Ubuntu with GTX 5090?
5. What's the calibration process?
6. Are there showstopper issues?
7. Has anyone used similar fisheye setup?
8. Can we extend to dual non-overlapping cameras?
9. Time estimate for deployment?
10. Overall: Is ORB-SLAM3 worth it vs alternatives?

---

## Timeline

**Estimated research time**: 6-8 hours

**Schedule**:
- Hour 1: Repository + README deep dive
- Hour 2: Academic paper review
- Hour 3: Kannala-Brandt + TUM VI analysis
- Hour 4: VI mode investigation
- Hour 5: Issue analysis + community health
- Hour 6: Multi-camera feasibility + modifications
- Hour 7: Community search + examples
- Hour 8: Report writing

---

## Notes

- **Priority**: MEDIUM-HIGH - State-of-the-art but may need single camera approach
- **Strength**: Most mature and cited SLAM system
- **Challenge**: Dual non-overlapping cameras not native
- **License**: GPLv3 - check if acceptable for your use case
- **Academic Gold Standard**: This is THE reference SLAM system
- **Community**: Huge community = lots of resources

---

## Key Resources

- **ORB-SLAM3 GitHub**: https://github.com/UZ-SLAMLab/ORB_SLAM3
- **Paper**: Campos et al. "ORB-SLAM3: An Accurate Open-Source Library for Visual, Visual-Inertial and Multi-Map SLAM" (2020)
- **TUM VI Dataset**: https://vision.in.tum.de/data/datasets/visual-inertial-dataset
- **EuRoC Dataset**: https://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets

Good luck! Determine if ORB-SLAM3's state-of-the-art accuracy justifies potential single-camera limitation. 🚀
