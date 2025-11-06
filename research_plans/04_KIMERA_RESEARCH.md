# Research Plan: Kimera for Insta360 Dual Fisheye Drone

## Mission Context

**Project**: Indoor drone SLAM with Insta360 dual fisheye camera + IMU
**Hardware**: GTX 5090, Ubuntu
**Camera**: Insta360 with two non-overlapping fisheye lenses (front & back)
**Sensors**: IMU available, extrinsic calibration known
**Requirements**: Minimum 1 FPS, preferably real-time (10-30 FPS)
**Approach for this system**: Visual-inertial SLAM with full mapping pipeline

---

## Why Kimera?

Kimera is identified as the **⭐ FULL SLAM SUITE** candidate:
- **Complete pipeline**: VIO + pose-graph SLAM + 3D meshing + semantic segmentation
- **Real-time on CPU** - no GPU required (but could leverage GPU)
- **MIT SPARK Lab backing** - cutting-edge research
- **Modular architecture** - can use components independently
- **Multi-camera extensions** available (MIT research 2023)
- **Tightly-coupled VI-SLAM** with IMU
- **ROS integration** - Robotic Operating System support
- **Dense 3D reconstruction** - goes beyond just localization

**Timeline estimate**: 2-4 weeks (depends on which modules we use)

---

## Research Objectives

Conduct a **deep technical investigation** of Kimera to assess if it's suitable for our Insta360 drone SLAM project, particularly evaluating:
1. Multi-camera support (recent research extensions)
2. Fisheye camera compatibility
3. Complete SLAM pipeline benefits vs complexity
4. Modular architecture - can we use just VIO or do we need full stack?
5. Academic project maturity and production-readiness

---

## Critical Questions to Answer

### 1. Can We Run This on Our Machine?
- [ ] **OS Compatibility**: Ubuntu versions supported? (18.04, 20.04, 22.04?)
- [ ] **ROS Requirements**: Which ROS distribution(s)?
- [ ] **GPU Requirements**: CPU-only confirmed, but can we leverage GTX 5090 for meshing/semantics?
- [ ] **CPU Requirements**: Multi-threading? Core count recommendations?
- [ ] **Dependencies**:
  - GTSAM (factor graph optimization)
  - OpenCV version
  - DBoW2, OpenGV, Kimera-RPGO
  - Mesh reconstruction libraries
- [ ] **Build System**: CMake + catkin complexity?
- [ ] **Performance metrics**: Published FPS numbers? Benchmarks?
- [ ] **Memory requirements**: RAM usage (VIO vs full pipeline)?

### 2. Repository Health & Community
- [ ] **GitHub Organization**: https://github.com/MIT-SPARK/Kimera
- [ ] **Submodules**: Kimera-VIO, Kimera-RPGO, Kimera-Semantics - stars/forks on each?
- [ ] **Last commit dates**: Active maintenance across all modules?
- [ ] **Contributors**: MIT SPARK Lab size and activity?
- [ ] **Releases**: Tagged versions? Stable releases?
- [ ] **License**: What type?
- [ ] **Academic papers**: Multiple papers? Citation counts?
- [ ] **Research group**: MIT SPARK Lab still active on this project?

### 3. Open Issues Relevant to Our Use Case
Search across Kimera repos for:
- [ ] "fisheye" - fisheye camera support
- [ ] "multi-camera" - multi-camera setup issues
- [ ] "insta360" - Insta360 mentions
- [ ] "non-overlapping" - non-overlapping camera discussions
- [ ] "drone" or "UAV" - drone deployment issues
- [ ] "performance" or "FPS" - performance problems
- [ ] "calibration" - calibration issues
- [ ] "Ubuntu 20" or "Ubuntu 22" - OS compatibility
- [ ] "IMU" - IMU integration issues

### 4. Documentation Quality
- [ ] **Main README**: Quality and completeness?
- [ ] **Wiki**: Comprehensive wiki available?
- [ ] **Installation guide**: Step-by-step?
- [ ] **Module documentation**: Docs for VIO, RPGO, Semantics, Mesher separately?
- [ ] **Config files**: Example configurations?
- [ ] **Camera models**: Which camera models supported? Fisheye?
- [ ] **Calibration guide**: Camera-IMU calibration process?
- [ ] **API documentation**: For integration?
- [ ] **Academic papers**: Are they good learning resources?
- [ ] **Tutorials**: Video tutorials or blog posts?

**Rating**: Excellent / Good / Fair / Poor

### 5. Setup Complexity
- [ ] **Modular vs monolithic**: Can we use just Kimera-VIO or need full stack?
- [ ] **ROS workspace**: How complex is the catkin setup?
- [ ] **Build process**: Number of repos to clone and build?
- [ ] **Dependencies**: How many? Installation difficulty?
- [ ] **Calibration**: What tools? Complexity level?
- [ ] **Configuration**: How many config files? Complexity?
- [ ] **Data input**: ROS topics, rosbags, live camera?

**Estimate setup time**: Simple (< 3 days) / Moderate (3-7 days) / Complex (1-3 weeks) / Very complex (3+ weeks)

### 6. Dockerized Version
- [ ] **Official Docker**: Dockerfile in repos?
- [ ] **Docker Hub**: Pre-built images?
- [ ] **Docker documentation**: Setup guides?
- [ ] **Community Docker images**: Third-party containers?
- [ ] **GPU support**: NVIDIA GPU passthrough for meshing?
- [ ] **Docker compose**: Multi-container setup?

### 7. Insta360-Specific Implementations
- [ ] **Kimera + Insta360**: Any projects combining them?
- [ ] **Kimera + fisheye**: Fisheye camera examples?
- [ ] **Kimera + 360 camera**: Omnidirectional camera usage?
- [ ] **Multi-camera Kimera**: Papers or projects with multi-camera (especially 2023 research)?
- [ ] **Research papers**: Papers using Kimera with wide-FOV cameras?
- [ ] **Community projects**: GitHub projects using Kimera on drones?

**Key search**: "Kimera multi-camera" - look for 2023 MIT paper on multi-camera VI-SLAM

### 8. Camera Input Options: Dual Fisheye Support
- [ ] **Native camera models**: What's supported? (Pinhole, fisheye models?)
- [ ] **Fisheye support**: Direct or via undistortion?
- [ ] **Multi-camera mode**: Does Kimera-VIO support multiple cameras natively?
- [ ] **2023 Multi-camera research**:
  - Paper: "Multi-Camera Visual-Inertial Simultaneous Localization and Mapping" (arXiv:2304.13182)
  - Code available?
  - Non-overlapping camera support confirmed?
- [ ] **Single fisheye option**: Can we run with one fisheye + IMU?
- [ ] **Configuration**: How to set up multi-camera rig?

**Determine our path**:
- Option A: Single fisheye (or stitched) + IMU → standard Kimera-VIO
- Option B: Dual fisheye multi-camera + IMU → use 2023 extension
- Option C: Not feasible with Insta360

---

## Investigation Steps

### Step 1: Repository Landscape (45 minutes)
1. Visit https://github.com/MIT-SPARK/Kimera
2. Understand the ecosystem:
   - Kimera-VIO (visual-inertial odometry)
   - Kimera-RPGO (pose-graph optimization)
   - Kimera-Semantics (semantic 3D mesh)
   - Kimera-Mesher (dense meshing)
3. Check stars, forks, activity on each
4. Read main README and architecture overview
5. Determine: Do we need all modules or just VIO?

### Step 2: Academic Paper Review (60 minutes)
1. Find main Kimera paper (Rosinol et al. 2020):
   - "Kimera: an Open-Source Library for Real-Time Metric-Semantic Localization and Mapping"
2. Find multi-camera extension paper (2023):
   - "Multi-Camera Visual-Inertial Simultaneous Localization and Mapping for Autonomous Valet Parking"
   - arXiv:2304.13182
3. Read abstracts and methodology
4. Check what sensors were used
5. Look for performance metrics
6. Check Google Scholar citation counts
7. Look for follow-up work using Kimera

### Step 3: Multi-Camera Deep Dive (60 minutes) ⭐ CRITICAL
1. Search for 2023 multi-camera research
2. Check if code is available (GitHub search: "kimera multi-camera")
3. Understand the architecture for multi-camera
4. Check if non-overlapping cameras are supported
5. Look for configuration examples
6. Assess integration difficulty with base Kimera

### Step 4: Issue Analysis (30 minutes)
1. Check issues across all Kimera repos
2. Search with keywords from Question 3
3. Check for fisheye-related issues
4. Look for multi-camera discussions
5. Assess maintainer responsiveness
6. Note any showstoppers

### Step 5: Documentation Review (45 minutes)
1. Read installation docs
2. Check for camera model documentation
3. Look for calibration guides (Kalibr integration?)
4. Find example configurations
5. Assess modular usage docs (can we use just VIO?)
6. Check ROS integration docs

### Step 6: Fisheye & Calibration (30 minutes)
1. Determine which camera models are supported
2. Check if fisheye requires undistortion or direct support
3. Find calibration requirements and tools
4. Look for fisheye calibration examples
5. Understand camera-IMU calibration process

### Step 7: Community & Examples (45 minutes)
1. GitHub search: "kimera vio" projects
2. YouTube: Kimera tutorials and demos
3. ROS Discourse: Kimera discussions
4. Reddit: r/ROS, r/robotics
5. Find real-world deployments
6. Look for drone applications specifically

### Step 8: Docker & Deployment (20 minutes)
1. Check for Dockerfiles in repos
2. Search Docker Hub
3. Look for community Docker images
4. Test if available

---

## Deliverables

Provide a **detailed report** with these sections:

### 1. Executive Summary (2-3 paragraphs)
- Is Kimera suitable for our Insta360 drone project?
- Key advantages and disadvantages
- Recommendation: Go/No-Go with confidence level

### 2. Architecture Overview (0.5 page)
- Kimera's modular components
- Which modules we need vs nice-to-have
- Full pipeline vs VIO-only approach

### 3. Multi-Camera Support Analysis (1-1.5 pages) ⭐ CRITICAL
- 2023 multi-camera research findings
- Code availability and maturity
- Non-overlapping camera support
- Configuration for dual fisheye
- Integration effort estimate

### 4. Fisheye Camera Compatibility (0.5-1 page)
- Which camera models supported
- Fisheye support (direct or undistorted)
- Calibration requirements
- Configuration complexity

### 5. Technical Feasibility (1 page)
- Ubuntu + ROS compatibility
- Hardware requirements vs our setup
- Expected performance (FPS)
- Dependencies and build complexity
- Modular usage feasibility

### 6. Repository Health (0.5 page)
- MIT SPARK Lab backing
- Activity levels across repos
- Maintenance status
- Community size

### 7. Critical Issues (0.5 page)
- Relevant open issues
- Showstoppers for our use case?
- Community responsiveness

### 8. Documentation Assessment (0.5 page)
- Quality rating
- Installation guides
- Multi-camera docs
- Calibration guides

### 9. Setup Complexity (1 page)
- Installation process
- Calibration requirements
- Configuration complexity
- Time estimate from zero to working

### 10. Deployment Options (0.5 page)
- Docker availability
- ROS integration
- Modular deployment (VIO only?)

### 11. Evidence for Insta360/Fisheye (0.5-1 page)
- Projects with wide-FOV cameras
- Multi-camera examples
- Confidence for our setup

### 12. Full Pipeline vs VIO-Only (0.5 page)
- Do we need loop closure, meshing, semantics?
- Performance impact of full pipeline
- Recommendation for our use case

### 13. Comparison with Alternatives (0.5 page)
- vs Stella VSLAM (simpler)
- vs Basalt (different optimization)
- vs VINS-Fusion (drone-proven)
- Why choose Kimera?

### 14. Next Steps (0.5-1 page)
- Deployment roadmap if we proceed
- Multi-camera integration plan
- Timeline estimate
- Risk mitigation

### 15. Resource Links
- All Kimera repos
- Academic papers
- Multi-camera research
- Community resources

---

## Success Criteria

✅ Can answer:
1. Does Kimera support multi-camera non-overlapping fisheye?
2. Is the 2023 multi-camera research code available?
3. Can we run just VIO or do we need full pipeline?
4. Is Kimera feasible on Ubuntu with our hardware?
5. What's the calibration complexity?
6. Are there showstopper issues?
7. Has anyone done similar setups?
8. Time estimate for deployment?
9. Overall: Is Kimera worth the complexity for our full SLAM needs?

---

## Timeline

**Estimated research time**: 6-8 hours

**Schedule**:
- Hour 1: Repository landscape + architecture
- Hour 2: Academic papers (main + multi-camera)
- Hour 3: Multi-camera research deep dive
- Hour 4: Issue analysis + documentation
- Hour 5: Fisheye support + calibration
- Hour 6: Community search + examples
- Hour 7: Docker + deployment options
- Hour 8: Report writing

---

## Notes

- **Priority**: MEDIUM-HIGH - Best if we want full SLAM (not just odometry)
- **Key Differentiator**: Complete pipeline with mapping, mesh, semantics
- **Critical Factor**: Multi-camera support (2023 research) must be assessed
- **Academic Project**: More research-oriented than VINS, assess production-readiness
- **Complexity**: Higher than alternatives, need to justify the value

---

## Key Resources

- **Kimera Index**: https://github.com/MIT-SPARK/Kimera
- **Kimera-VIO**: https://github.com/MIT-SPARK/Kimera-VIO
- **MIT SPARK Lab**: http://web.mit.edu/sparklab/
- **Multi-Camera Paper**: arXiv:2304.13182
- **Main Paper**: Rosinol et al. ICRA 2020

Good luck! Assess if Kimera's full SLAM capabilities justify the extra complexity. 🚀
