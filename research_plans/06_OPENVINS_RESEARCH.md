# Research Plan: OpenVINS for Insta360 Dual Fisheye Drone

## Mission Context

**Project**: Indoor drone SLAM with Insta360 dual fisheye camera + IMU
**Hardware**: GTX 5090, Ubuntu
**Camera**: Insta360 with two non-overlapping fisheye lenses (front & back)
**Sensors**: IMU available, extrinsic calibration known
**Requirements**: Minimum 1 FPS, preferably real-time (10-30 FPS)
**Approach for this system**: Filter-based VIO with Kannala-Brandt fisheye

---

## Why OpenVINS?

OpenVINS is the **FILTER-BASED ALTERNATIVE** (EKF instead of optimization):
- **Multi-camera VIO** support
- **Kannala-Brandt fisheye model** (same as ORB-SLAM3)
- **EKF-based** - Extended Kalman Filter instead of bundle adjustment
- **Potentially faster** than optimization-based methods
- **Excellent documentation** - known for good docs
- **Active development** - RPNG lab (Robot Perception and Navigation Group)
- **Modular and extensible** design
- **Supports stereo, multi-camera, and monocular + IMU**

**Timeline estimate**: 2-3 weeks

---

## Research Objectives

Conduct a **technical investigation** of OpenVINS to assess suitability for Insta360 + IMU drone:
1. Multi-camera fisheye support (non-overlapping)
2. Kannala-Brandt model performance
3. EKF vs optimization trade-offs for our use case
4. Documentation quality (reportedly excellent)
5. Filter-based advantages for real-time drone deployment

---

## Critical Questions to Answer

### 1. Can We Run This on Our Machine?
- [ ] **OS**: Ubuntu support? Versions?
- [ ] **ROS**: Which ROS versions?
- [ ] **GPU**: Can leverage GTX 5090 or CPU-only?
- [ ] **Dependencies**: OpenCV, Eigen, Ceres, Boost?
- [ ] **Performance**: Published FPS numbers?
- [ ] **Memory**: RAM requirements?

### 2. Repository Health
- [ ] **GitHub**: https://github.com/rpng/open_vins
- [ ] **Stars/Forks**: Popularity?
- [ ] **Activity**: Last commits, releases?
- [ ] **RPNG backing**: University of Delaware research lab?
- [ ] **License**: Type?
- [ ] **Papers**: Academic publications?

### 3. Open Issues Relevant to Us
- [ ] "fisheye" - fisheye issues
- [ ] "multi-camera" - multi-camera setup
- [ ] "Kannala-Brandt" - camera model
- [ ] "non-overlapping" - non-overlapping cameras
- [ ] "drone" - drone applications
- [ ] "performance" - performance issues

### 4. Documentation Quality
- [ ] **Docs website**: Quality of documentation site?
- [ ] **Installation**: Step-by-step guide?
- [ ] **Configuration**: Camera model docs?
- [ ] **Calibration**: Process and tools?
- [ ] **Examples**: Multi-camera examples?

**Rating**: Excellent / Good / Fair / Poor

### 5. Setup Complexity
- [ ] **Build**: How many steps?
- [ ] **Dependencies**: Installation difficulty?
- [ ] **Calibration**: Process complexity?
- [ ] **Configuration**: Config file complexity?

**Estimate**: Simple / Moderate / Complex

### 6. Dockerized Version
- [ ] Official or community Docker?
- [ ] Docker Hub availability?
- [ ] GPU support?

### 7. Insta360-Specific
- [ ] OpenVINS + Insta360 projects?
- [ ] Fisheye examples?
- [ ] Multi-camera examples?

### 8. Camera Options
- [ ] **Multi-camera**: Non-overlapping support confirmed?
- [ ] **Kannala-Brandt**: Full support?
- [ ] **Configuration**: Dual fisheye setup?
- [ ] **Single fisheye**: Monocular + IMU?

**Options**:
- A: Single fisheye + IMU
- B: Dual non-overlapping fisheye + IMU
- C: Not feasible

---

## Investigation Steps

### Step 1: Repository Analysis (30 min)
1. Visit https://github.com/rpng/open_vins
2. Read README
3. Check documentation website
4. Review file structure
5. Check GitHub insights

### Step 2: Documentation Deep Dive (60 min)
1. Read installation docs
2. Check camera model documentation
3. Look for multi-camera guides
4. Find calibration documentation
5. Review example configurations

### Step 3: Multi-Camera Analysis (45 min) ⭐ CRITICAL
1. Find multi-camera documentation
2. Look for non-overlapping camera examples
3. Check configuration requirements
4. Assess dual fisheye feasibility

### Step 4: Filter vs Optimization (30 min)
1. Understand EKF approach
2. Compare with optimization-based (VINS, Basalt)
3. Assess speed vs accuracy trade-offs
4. Check if suitable for drone real-time needs

### Step 5: Issue Analysis (30 min)
1. Search issues with keywords
2. Check for fisheye/multi-camera issues
3. Assess maintainer responsiveness

### Step 6: Community Search (45 min)
1. GitHub: "openvins" projects
2. YouTube: OpenVINS tutorials
3. Papers: Using OpenVINS
4. ROS Discourse

### Step 7: Calibration & Config (30 min)
1. Find Kannala-Brandt calibration examples
2. Check calibration tools
3. Look for multi-camera config examples

---

## Deliverables

### 1. Executive Summary (2 paragraphs)
- Is OpenVINS suitable?
- Recommendation with confidence

### 2. Multi-Camera Support (1 page) ⭐ CRITICAL
- Non-overlapping camera support?
- Configuration for dual fisheye
- Evidence of similar setups

### 3. EKF Approach Analysis (0.5 page)
- Filter-based vs optimization pros/cons
- Speed advantages?
- Accuracy trade-offs?
- Better for drones?

### 4. Fisheye Support (0.5 page)
- Kannala-Brandt implementation
- Calibration process
- Example configs

### 5. Technical Feasibility (0.5 page)
- Ubuntu + hardware compatibility
- Expected performance
- Build complexity

### 6. Documentation Assessment (0.5 page)
- Quality rating (reportedly excellent)
- Coverage of multi-camera setup

### 7. Repository Health (0.25 page)
- Activity and maintenance
- RPNG backing

### 8. Issues & Community (0.5 page)
- Relevant issues
- Community support

### 9. Setup Complexity (0.5 page)
- Installation steps
- Calibration
- Time estimate

### 10. Evidence for Our Setup (0.5 page)
- Similar configurations
- Confidence level

### 11. Comparison (0.5 page)
- vs VINS-Fusion (optimization-based)
- vs Basalt
- Why OpenVINS?

### 12. Next Steps (0.5 page)
- Deployment roadmap
- Timeline
- Risks

---

## Success Criteria

✅ Can answer:
1. Multi-camera non-overlapping support?
2. Kannala-Brandt for Insta360?
3. EKF benefits for real-time?
4. Documentation quality confirmed?
5. Feasible on our hardware?
6. Setup complexity?
7. Community examples?
8. Deployment timeline?

---

## Timeline

**Research time**: 4-5 hours

**Schedule**:
- Hour 1: Repository + documentation
- Hour 2: Multi-camera analysis
- Hour 3: EKF approach + comparison
- Hour 4: Community + examples
- Hour 5: Report writing

---

## Notes

- **Priority**: MEDIUM - Alternative to optimization-based
- **Key Feature**: EKF may be faster than optimization
- **Documentation**: Known for excellent docs - verify
- **Multi-Camera**: Critical to confirm non-overlapping support

---

## Key Resources

- **OpenVINS GitHub**: https://github.com/rpng/open_vins
- **Documentation**: Check for dedicated docs site
- **RPNG Lab**: University of Delaware

Good luck! Assess if OpenVINS's filter-based approach offers advantages. 🚀
