# Research Plan: Basalt VIO for Insta360 Dual Fisheye Drone

## Mission Context

**Project**: Indoor drone SLAM with Insta360 dual fisheye camera + IMU
**Hardware**: GTX 5090, Ubuntu
**Camera**: Insta360 with two non-overlapping fisheye lenses (front & back)
**Sensors**: IMU available, extrinsic calibration known
**Requirements**: Minimum 1 FPS, preferably real-time (10-30 FPS)
**Approach for this system**: Dual fisheye as multi-camera rig + tightly-coupled IMU integration

---

## Why Basalt?

Basalt is identified as **⭐ BEST MULTI-CAMERA FISHEYE VIO** candidate:
- **Explicit multi-camera support** with non-overlapping cameras
- **Double Sphere camera model** - designed for wide-FOV fisheye (180°-250°)
- **Tightly-coupled visual-inertial** optimization via factor graphs
- **Real-time on CPU** with multi-threaded bundle adjustment
- **Tested on TUM VI benchmark** (fisheye cameras + IMU)
- **APT packages available** for Ubuntu (easy installation)
- **Loop closure support** via mapping module
- **Calibration tools included** (compatible with Kalibr)

**Timeline estimate**: 2-3 weeks to working system (including calibration)

---

## Research Objectives

Your task is to conduct a **deep technical investigation** of Basalt to determine if it's the right choice for our dual fisheye + IMU drone SLAM project. You will assess:
1. Multi-camera fisheye support and Double Sphere model capabilities
2. Ubuntu + GPU compatibility for our hardware
3. Repository quality and academic backing
4. Setup and calibration complexity
5. IMU integration requirements
6. Deployment options and documentation quality

---

## Critical Questions to Answer

### 1. Can We Run This on Our Machine?

**Investigate:**
- [ ] **OS Compatibility**: Ubuntu support? Which versions? (Check for APT packages)
- [ ] **GPU Requirements**: Does Basalt leverage GPU? Will GTX 5090 help performance?
- [ ] **CPU Requirements**: Multi-threading support? How many cores utilized?
- [ ] **Dependencies**: What are the system requirements?
  - Eigen3
  - OpenCV
  - Pangolin (visualization)
  - TBB (threading)
  - Ceres Solver or similar?
- [ ] **Build System**: CMake requirements, compiler version (C++11 confirmed)
- [ ] **APT Packages**: Availability of pre-built Ubuntu packages (claimed in research)
- [ ] **Performance metrics**: Published benchmarks on TUM VI dataset? FPS numbers?
- [ ] **Memory requirements**: RAM usage for VIO vs full SLAM?

**Key check:**
- Is there actually an APT repository for easy installation?
- Or do we need to build from source?

### 2. Repository Health & Academic Backing

**Investigate:**
- [ ] **Primary repository**: https://gitlab.com/VladyslavUsenko/basalt (main)
- [ ] **GitHub mirror**: https://github.com/VladyslavUsenko/basalt (check if updated)
- [ ] **Stars/Forks**: On both GitLab and GitHub
- [ ] **Last commit date**: Active maintenance?
- [ ] **Contributors**: Academic lab (TUM CVG) or individual?
- [ ] **Releases**: Tagged versions? Stable release vs dev branch?
- [ ] **License**: What license type?
- [ ] **Academic paper**: Is there a paper? Where published? Citation count?
- [ ] **Research group**: TUM Computer Vision Group backing - still active?

**Academic credibility**:
- Find the Basalt paper (Usenko et al. 2018/2019)
- Check citation count on Google Scholar
- Check if used in other academic work

### 3. Open Issues Relevant to Our Use Case

**Search GitLab/GitHub issues for:**
- [ ] "multi-camera" - issues with multiple camera setups?
- [ ] "fisheye" - fisheye-specific issues?
- [ ] "Double Sphere" - camera model issues?
- [ ] "non-overlapping" - issues with non-overlapping camera setups?
- [ ] "IMU" - IMU integration issues?
- [ ] "calibration" - calibration problems?
- [ ] "performance" or "FPS" - performance-related issues?
- [ ] "Ubuntu" - OS-specific issues?
- [ ] "Insta360" - any mentions of Insta360 cameras?
- [ ] "drone" or "UAV" - any drone-specific discussions?

**Issue analysis:**
- Open vs closed ratio
- Maintainer responsiveness
- Any critical bugs that would block us?

### 4. Documentation Quality

**Assess:**
- [ ] **README**: Comprehensive installation and usage guide?
- [ ] **Wiki/Docs site**: Dedicated documentation?
- [ ] **Installation guide**: Step-by-step for Ubuntu? APT installation steps?
- [ ] **Camera models**: Documentation on Double Sphere model and when to use it?
- [ ] **Multi-camera setup**: How to configure multiple cameras?
- [ ] **Calibration guide**:
  - Camera intrinsic calibration process
  - Camera-IMU extrinsic calibration
  - Multi-camera relative calibration
- [ ] **Configuration files**: Example configs for fisheye cameras?
- [ ] **IMU parameters**: How to set IMU noise parameters?
- [ ] **API documentation**: For integration into our system?
- [ ] **Academic paper**: Is the paper accessible? Does it explain the system well?
- [ ] **Tutorials**: Community tutorials or blog posts?

**Documentation rating**:
- Excellent: Comprehensive with multi-camera examples
- Good: Sufficient to configure our setup
- Fair: Basic docs, requires experimentation
- Poor: Minimal docs, difficult to use

### 5. Setup Complexity

**Investigate:**
- [ ] **Installation methods**:
  - Option A: APT package installation (if available)
  - Option B: Build from source
- [ ] **Build process**: How many steps if building from source?
- [ ] **Dependencies**: How many? Easy to install or need manual building?
- [ ] **Calibration process**:
  - What calibration target needed? (AprilTag, checkerboard?)
  - Calibration software included or use Kalibr?
  - How many calibration sequences needed?
  - Estimated calibration time?
- [ ] **Configuration complexity**:
  - Config file format (YAML, JSON, custom?)
  - Example configs for dual fisheye?
  - How to specify camera models?
- [ ] **Data input format**:
  - What formats accepted? (ROS bag, image sequences, video?)
  - Live camera integration complexity?
- [ ] **IMU integration**:
  - IMU data format requirements
  - Synchronization requirements with cameras
  - Hardware trigger needed or software timestamps OK?

**Estimate setup time**:
- Simple: < 2 days
- Moderate: 2-5 days
- Complex: 1-2 weeks
- Very complex: 2+ weeks

### 6. Dockerized Version

**Investigate:**
- [ ] **Official Docker**: Does the repo provide a Dockerfile?
- [ ] **Docker Hub**: Pre-built images available?
- [ ] **Docker documentation**: Installation and usage guide for Docker?
- [ ] **Community Docker images**: Third-party Docker images?
- [ ] **Docker GPU support**: NVIDIA GPU passthrough for visualization?
- [ ] **Docker compose**: docker-compose.yml available?
- [ ] **ROS Docker**: Is there a ROS+Basalt Docker image?

**Test if possible**:
- Can you pull and test a Docker image?
- Does it include visualization tools (Pangolin)?

### 7. Insta360-Specific Implementations

**Search for:**
- [ ] **Basalt + Insta360**: GitHub/GitLab search for projects using both
- [ ] **Dual fisheye examples**: Example configs or projects with dual fisheye (non-overlapping)
- [ ] **Research papers**: Academic work using Basalt with fisheye cameras
  - Check Google Scholar: "Basalt VIO" AND "fisheye"
  - Check for TUM VI dataset papers (uses fisheye)
- [ ] **360 camera projects**: Anyone using 360° cameras with Basalt?
- [ ] **Community discussions**: Forum posts, Reddit, ROS Discourse
- [ ] **Calibration examples**: Example calibration data for fisheye cameras

**Broader search**:
- Search: "basalt vio multi-camera"
- Search: "basalt vio fisheye"
- Search: "double sphere camera model" + examples

### 8. Camera Input Options: Dual Fisheye vs Single

**Investigate:**
- [ ] **Native multi-camera support**: Confirmed? How many cameras supported?
- [ ] **Double Sphere model**:
  - What FOV does it support? (180°-250° claimed)
  - Calibration requirements?
  - Accuracy for Insta360 fisheye lenses?
- [ ] **Non-overlapping cameras**: Explicitly supported or need modification?
- [ ] **Camera configurations tested**:
  - What configs has Basalt been tested with?
  - Any examples with front+back cameras like our setup?
- [ ] **Alternative: Single fisheye**: Can we run with just one fisheye + IMU?
- [ ] **Stereo vs multi-mono**: Does Basalt treat multi-camera as:
  - Multi-monocular (independent tracks per camera)?
  - Some form of wide-baseline stereo?

**Our options**:
- Option A: Both fisheye cameras (front + back) + IMU
- Option B: Single fisheye (front or back) + IMU
- Option C: Not compatible with our setup

**Determine:**
- Which option is best supported?
- Configuration complexity for each option?

---

## Investigation Steps

### Step 1: Repository Analysis (30 minutes)
1. Visit GitLab: https://gitlab.com/VladyslavUsenko/basalt
2. Clone the repository
3. Read README.md thoroughly
4. Check docs/ directory if present
5. Examine CMakeLists.txt for dependencies
6. Check GitLab insights (stars, forks, activity)
7. Compare with GitHub mirror: https://github.com/VladyslavUsenko/basalt

### Step 2: Paper Review (45 minutes)
1. Find the Basalt research paper (Usenko et al.)
   - Search: "Basalt Visual-Inertial Mapping Non-Linear Factor Recovery"
2. Read abstract and methodology section
3. Check what camera models and datasets were used
4. Look for performance metrics (FPS, accuracy)
5. Check Google Scholar for citation count and follow-up work
6. Look for papers citing Basalt with fisheye cameras

### Step 3: Issue Analysis (30 minutes)
1. Go to GitLab Issues tab
2. Search with keywords listed in Question 3
3. Sort by most commented/recent
4. Check closed issues for resolved problems
5. Note maintainer response time and quality
6. Identify any blockers

### Step 4: Documentation Deep Dive (60 minutes)
1. Read installation documentation
2. Find camera model documentation (Double Sphere)
3. Look for calibration guides (check if Kalibr integration exists)
4. Search for multi-camera configuration examples
5. Read IMU integration docs
6. Look for config file examples
7. Check for API documentation

### Step 5: APT Package Investigation (20 minutes)
1. Search for "basalt apt package" or "basalt ubuntu package"
2. Check if there's a PPA or official package repo
3. Test installation if possible:
   ```bash
   # Check if this works:
   sudo apt search basalt
   ```
4. If APT package exists, check what version is available
5. Compare package version with latest GitLab release

### Step 6: Multi-Camera & Calibration Analysis (45 minutes)
1. Search codebase for:
   - "multi-camera" or "multicamera"
   - "camera_num" or "num_cameras"
   - Config examples with multiple cameras
2. Find calibration tools:
   - Look in scripts/ or tools/ directories
   - Check for calibration examples
3. Search for "Double Sphere" in code and docs
4. Check if Kalibr calibration files can be imported
5. Look for example calibration data

### Step 7: Community & Insta360 Search (60 minutes)
1. **GitLab/GitHub search**:
   - "basalt insta360"
   - "basalt dual fisheye"
   - "basalt multi-camera"
2. **Google Scholar**: "Basalt VIO" + "fisheye" or "omnidirectional"
3. **ROS Discourse**: Search for Basalt discussions
4. **Reddit**: r/computervision, r/robotics
5. **YouTube**: "basalt vio tutorial"
6. **TUM VI dataset**: Look at papers using this dataset with Basalt
7. **Personal websites**: Search for researchers who've used Basalt

### Step 8: Docker Investigation (20 minutes)
1. Check for Dockerfile in repo
2. Search Docker Hub: https://hub.docker.com/search?q=basalt
3. GitHub search: "basalt vio docker"
4. Check for ROS+Basalt Docker images
5. Test pulling and running if available

### Step 9: Alternative Models Comparison (20 minutes)
1. Compare Double Sphere vs other fisheye models:
   - Kannala-Brandt
   - EUCM
   - Mei's model
2. Understand when Double Sphere is preferred
3. Check if Basalt supports other models or only Double Sphere

---

## Deliverables

Please provide a **detailed report** with the following sections:

### 1. Executive Summary (2-3 paragraphs)
- Overall assessment: Is Basalt VIO the right choice for our dual fisheye + IMU drone?
- Key strengths and weaknesses for our specific use case
- Go/No-Go recommendation with confidence level

### 2. Technical Feasibility (1-1.5 pages)
- Hardware compatibility (Ubuntu + GTX 5090)
- Multi-camera fisheye support (critical!)
- IMU integration requirements
- Expected performance (FPS estimate)
- Dependencies and build complexity
- APT package availability (critical for easy install)

### 3. Multi-Camera Support Analysis (1 page) ⭐ CRITICAL
- Can Basalt handle two non-overlapping fisheye cameras?
- Configuration requirements for dual fisheye setup
- Evidence of similar setups in the wild
- Calibration complexity for multi-camera + IMU rig

### 4. Double Sphere Camera Model (0.5 page)
- What is it and why is it better for fisheye?
- FOV coverage (will it work for Insta360 lenses?)
- Calibration process for this model
- Comparison with other fisheye models

### 5. Repository Health (0.5 page)
- GitLab vs GitHub - which is primary?
- Stars, forks, activity level
- Academic backing (TUM CVG)
- Maintenance status and community

### 6. Critical Issues (0.5-1 page)
- Relevant open issues with links
- Any showstoppers for dual fisheye setup?
- Community responsiveness

### 7. Documentation Assessment (0.5 page)
- Quality rating with justification
- What's well documented?
- What's missing or unclear?
- Multi-camera documentation specifically

### 8. Setup & Calibration Complexity (1 page)
- Installation process (APT vs source build)
- Calibration requirements and process
- Configuration complexity
- Estimated time from zero to working system

### 9. Deployment Options (0.5 page)
- APT package (if available)
- Docker (if available)
- Build from source process
- Recommended deployment method

### 10. Insta360/Dual Fisheye Evidence (1 page) ⭐ CRITICAL
- Has anyone used Basalt with similar camera setup?
- Links to examples, papers, projects
- Evidence of non-overlapping multi-camera success
- Confidence that our setup will work

### 11. Performance Expectations (0.5 page)
- Expected FPS with dual fisheye + IMU
- CPU vs GPU utilization
- Memory requirements
- Comparison with benchmarks (TUM VI)

### 12. IMU Integration (0.5 page)
- Tightly vs loosely coupled (confirm tight coupling)
- IMU requirements and parameters
- Time synchronization requirements
- Expected improvement from IMU

### 13. Comparison with Alternatives (0.5 page)
Brief comparison with:
- Stella VSLAM (omnidirectional approach)
- VINS-Fusion (alternative VI-SLAM)
- Why choose Basalt over these?

### 14. Next Steps (0.5 page)
If we proceed with Basalt:
- Detailed deployment roadmap
- Calibration preparation (equipment needed)
- Timeline estimate (realistic)
- Risk mitigation strategies

### 15. Resource Links
- Paper links
- Repository links
- Example projects/configs
- Tutorial links
- Calibration tools

---

## Success Criteria

Your research is successful if you can confidently answer:
1. ✅ Can Basalt handle our dual non-overlapping fisheye + IMU setup?
2. ✅ Does the Double Sphere model work for Insta360 lenses?
3. ✅ Can we run it on Ubuntu with GTX 5090?
4. ✅ Is there an easy installation method (APT package)?
5. ✅ Is the calibration process documented and feasible?
6. ✅ Are there any showstopper issues?
7. ✅ Has anyone done something similar successfully?
8. ✅ What's the estimated time to deployment?
9. ✅ Overall: Is Basalt worth the extra complexity vs Stella VSLAM?

---

## Timeline

**Estimated research time**: 5-7 hours for thorough investigation

**Suggested schedule**:
- Hour 1: Repository + README review
- Hour 2: Academic paper review + camera model understanding
- Hour 3: Multi-camera support deep dive
- Hour 4: Issue analysis + community health
- Hour 5: Calibration & configuration complexity assessment
- Hour 6: Insta360/dual fisheye evidence search
- Hour 7: Report writing with detailed recommendations

---

## Notes

- **Priority**: HIGH PRIORITY - Basalt is the top choice for tightly-coupled multi-camera VI-SLAM
- **Critical Factor**: Multi-camera support with non-overlapping cameras must be confirmed
- **Calibration**: This will be more complex than Stella VSLAM - assess if we can handle it
- **Comparison**: Keep in mind the trade-off: Basalt (higher accuracy, complex) vs Stella (faster deployment, simpler)
- **Evidence**: We need strong evidence that dual fisheye non-overlapping setup will work

---

## Key Resources

- **Primary GitLab Repo**: https://gitlab.com/VladyslavUsenko/basalt
- **GitHub Mirror**: https://github.com/VladyslavUsenko/basalt
- **TUM VI Dataset** (used for testing): https://vision.in.tum.de/data/datasets/visual-inertial-dataset
- **Kalibr** (calibration tool): https://github.com/ethz-asl/kalibr
- **Research group**: TUM Computer Vision Group

Good luck with your investigation! This is a critical decision point for our project. 🚀
