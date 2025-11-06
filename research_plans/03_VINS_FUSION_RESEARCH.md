# Research Plan: VINS-Fusion/VINS-Fisheye for Insta360 Dual Fisheye Drone

## Mission Context

**Project**: Indoor drone SLAM with Insta360 dual fisheye camera + IMU
**Hardware**: GTX 5090, Ubuntu
**Camera**: Insta360 with two non-overlapping fisheye lenses (front & back)
**Sensors**: IMU available, extrinsic calibration known
**Requirements**: Minimum 1 FPS, preferably real-time (10-30 FPS)
**Approach for this system**: Single or dual fisheye + tightly-coupled IMU

---

## Why VINS-Fusion/VINS-Fisheye?

VINS-Fusion is identified as **⭐ BEST FOR DRONE DEPLOYMENT** candidate:
- **Industry-proven on aerial robots** - HKUST Aerial Robotics Group (drone specialists)
- **Native fisheye support** via equidistant and Mei's omnidirectional camera models
- **Dedicated fisheye fork** - VINS-Fisheye specifically for wide-FOV cameras
- **Tightly-coupled visual-inertial** optimization via Ceres Solver
- **Real-time performance**: 10-30 FPS on modern CPU (no GPU required)
- **Loop closure optional** (can enable/disable for performance)
- **Mature and well-documented** - widely used in robotics community
- **ROS integration** - easy integration into robot stacks
- **Can extend to multi-camera** (research has demonstrated non-overlapping setups)

**Timeline estimate**: 2-3 weeks (single camera), 3-4 weeks (dual camera extension)

---

## Research Objectives

Your task is to conduct a **deep technical investigation** of VINS-Fusion and VINS-Fisheye to determine if they are the right choice for our Insta360 + IMU drone project. You will assess:
1. Fisheye camera model support (equidistant, Mei's model, compatibility with Insta360)
2. Single vs dual fisheye configuration options
3. Drone-specific performance and reliability
4. Repository health and community support (HKUST backing)
5. Setup and calibration complexity
6. Multi-camera extensibility

---

## Critical Questions to Answer

### 1. Can We Run This on Our Machine?

**Investigate:**
- [ ] **OS Compatibility**: Ubuntu versions supported? (ROS compatibility)
- [ ] **ROS Requirements**: Which ROS distribution? (Kinetic/Melodic/Noetic?)
- [ ] **GPU Requirements**: CPU-only or can leverage GTX 5090?
- [ ] **CPU Requirements**: Multi-threading? Core utilization?
- [ ] **Dependencies**:
  - Ceres Solver
  - OpenCV (version requirements)
  - Eigen
  - ROS packages
- [ ] **Build System**: CMake, catkin workspace setup
- [ ] **Performance metrics**: Published FPS numbers on drones?
- [ ] **Memory requirements**: RAM usage during flight?
- [ ] **Real-world drone tests**: Any published flight test results?

**Key checks:**
- ROS version compatibility with Ubuntu version
- Can it run without ROS? (some projects offer non-ROS builds)

### 2. Repository Health & Community

**Investigate VINS-Fusion:**
- [ ] **GitHub**: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion
- [ ] **Stars/Forks**: How popular?
- [ ] **Last commit**: Active maintenance?
- [ ] **Contributors**: HKUST Aerial Robotics lab + community
- [ ] **Releases**: Tagged stable releases?
- [ ] **License**: What type?
- [ ] **Academic backing**: HKUST research group - still active?

**Investigate VINS-Fisheye:**
- [ ] **GitHub**: https://github.com/HKUST-Aerial-Robotics/VINS-Fisheye
- [ ] **Relation to VINS-Fusion**: Fork or separate project?
- [ ] **Maintenance**: More or less active than VINS-Fusion?
- [ ] **Stars/Forks**: Popularity compared to main VINS-Fusion
- [ ] **Recommendation**: Which should we use? Main or Fisheye fork?

**Investigate VINS-Mono** (predecessor):
- [ ] **GitHub**: https://github.com/HKUST-Aerial-Robotics/VINS-Mono
- [ ] **Relation**: Is VINS-Fusion a successor?
- [ ] **Should we consider it?**: Or is VINS-Fusion strictly better?

**Academic papers**:
- Find VINS-Mono paper (Qin et al. 2018?)
- Find VINS-Fusion paper (Qin et al. 2019?)
- Citation counts?
- Used in other academic work?

### 3. Open Issues Relevant to Our Use Case

**Search GitHub issues across all VINS repos for:**
- [ ] "fisheye" - fisheye camera issues
- [ ] "equidistant" or "Mei" - camera model issues
- [ ] "insta360" - any Insta360-specific mentions
- [ ] "multi-camera" or "dual camera" - multi-camera setup issues
- [ ] "non-overlapping" - non-overlapping camera discussions
- [ ] "drone" or "UAV" or "quadcopter" - drone-specific issues
- [ ] "indoor" - indoor flight issues
- [ ] "performance" or "FPS" - performance problems
- [ ] "calibration" - calibration issues
- [ ] "IMU" - IMU-related problems
- [ ] "Ubuntu 20" or "Ubuntu 22" - recent Ubuntu versions

**Issue analysis:**
- Are issues actively triaged and resolved?
- Community helping each other?
- Maintainer responsiveness?
- Any critical bugs for our use case?

### 4. Documentation Quality

**Assess VINS-Fusion:**
- [ ] **README**: Quality and completeness?
- [ ] **Installation guide**: Step-by-step for Ubuntu + ROS?
- [ ] **Wiki**: Is there a wiki with detailed info?
- [ ] **Config files**: Example configurations for fisheye?
- [ ] **Camera models**: Documentation on supported camera models?
- [ ] **Calibration guide**:
  - Camera intrinsic calibration
  - Camera-IMU extrinsic calibration
  - Recommended calibration tools (Kalibr?)
- [ ] **Parameter tuning**: How to tune for different scenarios?
- [ ] **ROS topics/services**: API documentation?
- [ ] **Troubleshooting**: Common issues and solutions?
- [ ] **Academic paper**: Is it accessible and helpful for understanding?

**Assess VINS-Fisheye:**
- [ ] How does documentation compare to VINS-Fusion?
- [ ] Fisheye-specific configuration examples?
- [ ] Additional setup steps vs main VINS-Fusion?

**Community resources:**
- [ ] Tutorials on YouTube
- [ ] Blog posts and guides
- [ ] ROS wiki or ROS Discourse threads

**Documentation rating**:
- Excellent: Comprehensive with drone examples
- Good: Sufficient to get started with fisheye
- Fair: Basic, requires some trial and error
- Poor: Minimal, difficult to configure

### 5. Setup Complexity

**Investigate:**
- [ ] **ROS setup**: How complex is catkin workspace setup?
- [ ] **Build process**: Number of steps? Common build errors?
- [ ] **Dependencies**: How many? APT installable or manual?
- [ ] **Calibration**:
  - Calibration target requirements (AprilTag, checkerboard?)
  - Kalibr integration (recommended tool?)
  - How many sequences needed?
  - Time estimate for calibration?
- [ ] **Configuration files**:
  - Config format (YAML?)
  - How to specify fisheye camera model?
  - Example configs for fisheye?
  - How to set IMU parameters?
- [ ] **Data input**:
  - ROS topics (image and IMU topics)
  - Can it run on recorded rosbags?
  - Live camera integration?
- [ ] **Loop closure**: How to enable/disable? Configuration?
- [ ] **Output**: Trajectory format? Visualization?

**Estimate setup time**:
- Simple: 1-2 days
- Moderate: 3-5 days
- Complex: 1-2 weeks
- Very complex: 2+ weeks

### 6. Dockerized Version

**Investigate:**
- [ ] **Official Docker**: Dockerfile in repo?
- [ ] **Docker Hub**: Pre-built images?
- [ ] **Community Docker images**: Third-party Dockerfiles?
- [ ] **ROS Docker**: Integration with ROS Docker images?
- [ ] **Docker GPU**: NVIDIA GPU support in containers?
- [ ] **Docker compose**: docker-compose.yml available?
- [ ] **Tutorials**: Docker-specific setup guides?

**Test if possible:**
- Pull and test a Docker image
- Verify it includes visualization tools (rviz)

### 7. Insta360-Specific Implementations

**Search for:**
- [ ] **VINS + Insta360**: GitHub search for projects combining them
- [ ] **VINS + 360 camera**: Any 360° camera projects
- [ ] **VINS + fisheye examples**: Example configs and datasets
- [ ] **Research papers**:
  - Google Scholar: "VINS-Fusion" AND "fisheye"
  - Google Scholar: "VINS" AND "omnidirectional"
  - Papers using VINS with wide-FOV cameras
- [ ] **Drone projects**: GitHub search "vins fusion drone" or "vins quadcopter"
- [ ] **Indoor drone**: Specific indoor navigation projects
- [ ] **Community forums**:
  - ROS Discourse
  - Reddit r/ROS, r/robotics
  - Chinese forums (HKUST is Chinese, check Zhihu, CSDN?)

**Calibration examples:**
- [ ] Fisheye calibration files shared by community
- [ ] Kalibr examples for fisheye + IMU

### 8. Camera Input Options: Single vs Dual Fisheye

**Investigate:**
- [ ] **VINS-Fusion default**:
  - Monocular + IMU?
  - Stereo + IMU?
  - Can it do both?
- [ ] **Fisheye camera models**:
  - Equidistant (radtan) model
  - Mei's omnidirectional model
  - Which one for Insta360 lenses?
- [ ] **Single fisheye + IMU**:
  - Is this the standard configuration?
  - Performance expectations?
  - Example configurations?
- [ ] **Dual fisheye possibilities**:
  - Stereo fisheye (if some overlap exists)?
  - Dual monocular (two independent VINS instances)?
  - Multi-camera extension (research has shown it's possible)?
  - Configuration complexity?
- [ ] **Multi-camera research**:
  - Search papers: "VINS multi-camera" or "VINS multiple cameras"
  - Has anyone extended VINS to non-overlapping cameras?
  - Code available for multi-camera VINS?

**Recommended approach for Insta360**:
- Option A: Single fisheye (front or back) + IMU → simplest
- Option B: Dual fisheye non-overlapping + IMU → requires extension/modification
- Option C: Stitch to omnidirectional, treat as single camera + IMU

**Determine:**
- Which option is officially supported?
- Which has community examples?
- Effort required for each option?

---

## Investigation Steps

### Step 1: Repository Overview (30 minutes)
1. Visit VINS-Fusion: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion
2. Visit VINS-Fisheye: https://github.com/HKUST-Aerial-Robotics/VINS-Fisheye
3. Read both READMEs thoroughly
4. Compare the two: which is recommended for fisheye?
5. Check GitHub insights (stars, forks, activity, commits)
6. Clone the recommended repo
7. Examine file structure and documentation

### Step 2: Academic Paper Review (45 minutes)
1. Find VINS-Mono and VINS-Fusion papers
   - Search: "VINS-Mono" and "VINS-Fusion" on Google Scholar
2. Read abstracts and methodology sections
3. Check camera models and datasets used
4. Look for performance metrics (FPS, accuracy on drones)
5. Check citation counts
6. Look for papers citing VINS with fisheye or omnidirectional cameras

### Step 3: Drone-Specific Analysis (45 minutes)
1. Search for drone projects using VINS:
   - GitHub: "vins fusion drone"
   - GitHub: "vins quadcopter"
   - YouTube: "vins drone"
2. Look for indoor drone projects specifically
3. Check HKUST Aerial Robotics Group website for project examples
4. Find flight test videos and performance data
5. Community discussions on drone deployment

### Step 4: Issue Analysis (30 minutes)
1. Search issues across VINS-Fusion, VINS-Fisheye, VINS-Mono
2. Use keyword search from Question 3
3. Sort by most commented and recent
4. Check closed issues for solutions to common problems
5. Note maintainer and community responsiveness
6. Identify any blockers for our use case

### Step 5: Documentation Deep Dive (60 minutes)
1. Read installation docs thoroughly
2. Find camera model documentation:
   - How to use equidistant model
   - How to use Mei's omnidirectional model
   - Which to choose for Insta360?
3. Calibration documentation:
   - Intrinsic calibration process
   - Extrinsic (camera-IMU) calibration
   - Recommended tools (Kalibr integration?)
4. Configuration files:
   - Find example configs
   - Look for fisheye-specific configs
5. Parameter tuning guides
6. ROS integration documentation

### Step 6: Fisheye Camera Model Analysis (45 minutes)
1. Understand the camera models:
   - Equidistant (radtan) projection
   - Mei's unified omnidirectional model
   - When to use each?
2. Find calibration examples for each model
3. Determine which model suits Insta360 lenses
4. Check if both VINS-Fusion and VINS-Fisheye support same models
5. Look for Insta360-specific calibration examples

### Step 7: Multi-Camera Extension Research (60 minutes)
1. Search academic literature:
   - "VINS multi-camera"
   - "VINS multiple cameras"
   - "VINS non-overlapping"
2. GitHub search for multi-camera VINS forks/extensions
3. Check if anyone has modified VINS for dual non-overlapping cameras
4. Assess feasibility of extending VINS for our dual fisheye setup
5. Estimate effort required for multi-camera extension

### Step 8: Docker & Deployment (20 minutes)
1. Check repo for Dockerfile
2. Search Docker Hub for VINS images
3. GitHub search: "vins fusion docker"
4. Test a Docker image if available
5. Check for ROS + VINS Docker images

### Step 9: Calibration Tools Investigation (30 minutes)
1. Check if Kalibr is recommended/required
2. Look for VINS-specific calibration tools
3. Find example calibration datasets or tutorials
4. Understand the calibration workflow:
   - Collect calibration data
   - Run calibration tool
   - Export to VINS format
5. Estimate time required for full calibration

### Step 10: Community & Tutorials (30 minutes)
1. YouTube: Search for VINS tutorials
2. ROS Discourse: Search for VINS threads
3. Reddit: r/ROS and r/robotics VINS posts
4. Chinese resources: Zhihu, CSDN (use Google Translate if needed)
5. Personal blogs: VINS setup guides
6. Find troubleshooting resources

---

## Deliverables

Please provide a **detailed report** with the following sections:

### 1. Executive Summary (2-3 paragraphs)
- Overall assessment: Is VINS-Fusion/Fisheye the right choice for our drone project?
- VINS-Fusion vs VINS-Fisheye - which to use?
- Key strengths and weaknesses
- Go/No-Go recommendation with confidence level

### 2. Technical Feasibility (1-1.5 pages)
- Ubuntu + ROS compatibility
- Hardware requirements vs our GTX 5090 setup
- Expected performance (FPS) on drone
- Dependencies and build complexity
- Single fisheye + IMU feasibility (primary option)
- Dual fisheye + IMU feasibility (advanced option)

### 3. Fisheye Camera Support (1 page) ⭐ CRITICAL
- Camera models supported (equidistant, Mei's omnidirectional)
- Which model for Insta360 lenses?
- VINS-Fusion vs VINS-Fisheye for our cameras
- Configuration requirements
- Calibration process for fisheye

### 4. Drone Deployment Evidence (1 page) ⭐ CRITICAL
- Real-world drone projects using VINS
- Indoor navigation examples
- Performance data from drone flights
- Reliability and robustness for drone use
- Community experience with drones

### 5. Repository Health (0.5 page)
- Stars, forks, activity across VINS repos
- HKUST Aerial Robotics Group backing
- Maintenance status
- Community size and engagement
- Which repo to use (Fusion vs Fisheye vs Mono)

### 6. Critical Issues (0.5-1 page)
- Relevant open issues with links
- Common problems and solutions
- Any showstoppers for fisheye + drone use?
- Community and maintainer responsiveness

### 7. Documentation Assessment (0.5-1 page)
- Quality rating with justification
- Installation and setup docs
- Fisheye camera configuration docs
- Calibration guides
- ROS integration docs
- Community tutorials and resources

### 8. Setup & Calibration Complexity (1 page)
- ROS workspace setup process
- Build and installation steps
- Calibration requirements:
  - Equipment needed
  - Process overview
  - Time estimate
- Configuration file setup
- Estimated time from zero to working system

### 9. Single vs Dual Fisheye Analysis (1 page) ⭐ CRITICAL
- Option A: Single fisheye + IMU
  - Officially supported?
  - Configuration complexity?
  - Expected performance?
- Option B: Dual non-overlapping fisheye + IMU
  - Possible with modification?
  - Existing examples or research?
  - Effort required?
- Recommended approach for Insta360

### 10. Multi-Camera Extension Research (0.5-1 page)
- Has anyone extended VINS to multi-camera non-overlapping?
- Research papers or projects found
- Feasibility assessment
- Effort estimate if we want dual fisheye

### 11. Deployment Options (0.5 page)
- Docker availability
- ROS integration
- Build from source
- Recommended deployment method

### 12. Insta360/Fisheye Evidence (1 page) ⭐ CRITICAL
- Projects using VINS with wide-FOV cameras
- Fisheye calibration examples
- Community success stories
- Links to examples and tutorials
- Confidence level for our setup

### 13. Performance Expectations (0.5 page)
- Expected FPS on our hardware
- CPU/GPU utilization
- Memory requirements
- Comparison with published benchmarks
- Indoor performance specifically

### 14. Comparison with Alternatives (0.5 page)
Brief comparison with:
- Stella VSLAM (simpler but no IMU)
- Basalt (multi-camera but more complex)
- Why choose VINS over these?

### 15. Next Steps (0.5-1 page)
If we proceed with VINS:
- Single fisheye or dual fisheye approach?
- Detailed deployment roadmap
- Calibration preparation
- Timeline estimate (realistic)
- Risk mitigation

### 16. Resource Links
- All VINS repository links
- Academic papers
- Example projects and configs
- Calibration tools and tutorials
- Community resources

---

## Success Criteria

Your research is successful if you can confidently answer:
1. ✅ VINS-Fusion or VINS-Fisheye - which is better for us?
2. ✅ Can we run single fisheye + IMU easily?
3. ✅ Can we extend to dual fisheye non-overlapping?
4. ✅ Which fisheye camera model for Insta360?
5. ✅ Is VINS proven for drone deployment?
6. ✅ Can we run it on Ubuntu with our hardware?
7. ✅ Is calibration process manageable?
8. ✅ Are there showstopper issues?
9. ✅ What's the estimated deployment time?
10. ✅ Overall: Is VINS the best choice for drone production system?

---

## Timeline

**Estimated research time**: 6-8 hours for thorough investigation

**Suggested schedule**:
- Hour 1: Repository overview + README comparison
- Hour 2: Academic papers + drone project search
- Hour 3: Fisheye camera model deep dive
- Hour 4: Issue analysis + documentation review
- Hour 5: Multi-camera extension research
- Hour 6: Community resources + calibration tools
- Hour 7: Insta360/fisheye evidence gathering
- Hour 8: Report writing with recommendations

---

## Notes

- **Priority**: HIGH - VINS is the proven choice for drone SLAM
- **Critical Decision**: Single vs dual fisheye approach
- **Drone Focus**: This is specifically designed for drones - big advantage
- **ROS Dependency**: We'll need ROS - assess if this is acceptable
- **Calibration**: Will require careful calibration - is this manageable?
- **Community**: Large community = good support

---

## Key Resources

- **VINS-Fusion**: https://github.com/HKUST-Aerial-Robotics/VINS-Fusion
- **VINS-Fisheye**: https://github.com/HKUST-Aerial-Robotics/VINS-Fisheye
- **VINS-Mono** (original): https://github.com/HKUST-Aerial-Robotics/VINS-Mono
- **HKUST Aerial Robotics**: http://uav.ust.hk/
- **Kalibr** (calibration): https://github.com/ethz-asl/kalibr

Good luck! This research will help us decide if VINS is our production-ready solution. 🚀
