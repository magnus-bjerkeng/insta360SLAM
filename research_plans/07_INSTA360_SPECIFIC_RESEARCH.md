# Research Plan: Insta360-Specific SLAM Implementations & Forks

## Mission Context

**Project**: Indoor drone SLAM with Insta360 dual fisheye camera + IMU
**Hardware**: GTX 5090, Ubuntu
**Camera**: Insta360 with two non-overlapping fisheye lenses (front & back)
**Sensors**: IMU available, extrinsic calibration known
**Requirements**: Minimum 1 FPS, preferably real-time (10-30 FPS)

---

## Research Objective

This is a **DISCOVERY MISSION** to find existing SLAM implementations, forks, modifications, and projects that were specifically built for or adapted to work with Insta360 cameras. The goal is to discover ready-made or nearly-ready solutions that save us implementation time.

**Why this matters**: Someone may have already solved our exact problem. Finding an Insta360-specific implementation could save us weeks of work.

---

## What We're Looking For

### 🎯 Primary Targets

1. **SLAM systems modified for Insta360**
   - Forks of ORB-SLAM, VINS, Basalt, etc. adapted for Insta360
   - Custom SLAM implementations for 360° cameras
   - Insta360 SDK integrations

2. **Drone + Insta360 projects**
   - UAV/quadcopter projects using Insta360 for navigation
   - Indoor drone SLAM with 360° cameras
   - Autonomous flight with Insta360

3. **Research projects & papers**
   - Academic research using Insta360 for SLAM/VIO
   - Thesis projects, dissertations
   - Conference/journal papers with code

4. **Commercial/industrial implementations**
   - Robotics companies using Insta360
   - Open-sourced production code
   - ROS packages for Insta360 SLAM

5. **Stitching & preprocessing pipelines**
   - Real-time stitching for Insta360 → SLAM
   - Insta360 SDK usage examples
   - Dual fisheye → equirectangular pipelines

---

## Critical Questions to Answer

### 1. GitHub/GitLab Repositories

**Search for:**
- [ ] "insta360 slam"
- [ ] "insta360 vio"
- [ ] "insta360 visual odometry"
- [ ] "insta360 orb-slam"
- [ ] "insta360 vins"
- [ ] "insta360 drone"
- [ ] "insta360 uav"
- [ ] "insta360 ros"
- [ ] "360 camera slam"
- [ ] "omnidirectional slam" + "insta360"
- [ ] Specific Insta360 models:
  - "insta360 one x2" slam
  - "insta360 one rs" slam
  - "insta360 pro" slam

**For each repository found, document:**
- [ ] Repository URL
- [ ] Stars/forks/activity level
- [ ] Last commit date
- [ ] What Insta360 model it supports
- [ ] What SLAM algorithm it uses
- [ ] Does it use single fisheye, dual fisheye, or stitched?
- [ ] Does it integrate IMU?
- [ ] Is it for drones or ground robots?
- [ ] Quality of documentation
- [ ] License
- [ ] Can it run on Ubuntu?
- [ ] Build complexity
- [ ] **Critical**: Can we use this for our setup?

### 2. Research Papers & Academic Projects

**Search platforms:**
- [ ] **Google Scholar**: "Insta360" + "SLAM" / "visual odometry" / "navigation"
- [ ] **arXiv**: Same keywords
- [ ] **IEEE Xplore**: Conference papers (ICRA, IROS, CVPR)
- [ ] **ACM Digital Library**: Robotics conferences
- [ ] **ResearchGate**: Direct researcher uploads
- [ ] **University repositories**: Thesis/dissertation databases

**For each paper found:**
- [ ] Paper title and authors
- [ ] Conference/journal and year
- [ ] What Insta360 model used
- [ ] What algorithm (original or modified existing?)
- [ ] Is code available? (GitHub link?)
- [ ] Results and performance
- [ ] Indoor or outdoor testing?
- [ ] Drone or ground robot?
- [ ] Can we replicate their setup?

### 3. Insta360 SDK & Official Resources

**Investigate:**
- [ ] **Insta360 Developer Portal**: Does one exist?
- [ ] **Insta360 SDK**:
  - Is there an official SDK?
  - What platforms? (Windows, Linux, mobile?)
  - Camera access APIs
  - Stitching APIs
  - IMU data access
  - Cost (free or paid?)
- [ ] **Insta360 GitHub**: Does Insta360 have official GitHub repos?
- [ ] **Official examples**: Sample code for robotics/SLAM applications?
- [ ] **Documentation**: Camera specs, calibration data, lens parameters

**Search for:**
- "Insta360 SDK documentation"
- "Insta360 developer"
- "Insta360 API"
- Official Insta360 GitHub organization

### 4. ROS Packages & Integrations

**Search for:**
- [ ] "insta360_ros" or similar ROS packages
- [ ] Insta360 camera drivers for ROS
- [ ] Insta360 SLAM launch files
- [ ] ROS wiki pages about Insta360
- [ ] ROS Discourse discussions about Insta360

**Document:**
- Package names and repositories
- ROS1 vs ROS2
- Features (camera driver only, or SLAM integration?)
- Image topics published (raw fisheye, stitched, etc.)
- IMU topics
- Quality and maintenance

### 5. Community Forums & Discussions

**Platforms to search:**
- [ ] **Reddit**:
  - r/computervision
  - r/robotics
  - r/ROS
  - r/drones
- [ ] **ROS Discourse**: Search "insta360"
- [ ] **Stack Overflow**: "insta360 slam", "insta360 opencv"
- [ ] **Robotics Stack Exchange**: Same keywords
- [ ] **Insta360 Community Forum**: If it exists
- [ ] **Chinese forums** (Insta360 is Chinese company):
  - Zhihu (知乎) - Chinese Quora
  - CSDN (Chinese developer community)
  - Use Google Translate

**Document:**
- Discussion threads with useful information
- People who have worked on Insta360 SLAM
- Challenges and solutions mentioned
- Links to projects or code

### 6. YouTube & Video Tutorials

**Search for:**
- [ ] "insta360 slam"
- [ ] "insta360 ros"
- [ ] "insta360 drone navigation"
- [ ] "360 camera slam"
- [ ] "omnidirectional slam tutorial"

**Document:**
- Videos showing Insta360 SLAM implementations
- Tutorial videos with code links
- Conference presentation videos
- Project demos
- Creators (can we contact them?)

### 7. Company & Lab Websites

**Search for robotics companies/labs using Insta360:**
- [ ] Search: "insta360" site:*.edu (university labs)
- [ ] Search: "insta360 slam" site:github.io (project pages)
- [ ] Robotics companies' blog posts about Insta360
- [ ] Autonomous vehicle companies

**Document:**
- Projects using Insta360
- Code repositories if public
- Technical blog posts
- Contact information

### 8. Specific Insta360 Models

**Which Insta360 model are we using?** (Clarify with team)

**Common models for robotics:**
- **Insta360 ONE X2**: Consumer 360° action camera
- **Insta360 ONE RS**: Modular, has 360° module
- **Insta360 Pro**: Professional 8K 360° (more expensive)
- **Insta360 EVO**: Foldable 180°/3D camera

**For our specific model, search:**
- "[Model] slam"
- "[Model] ros"
- "[Model] drone"
- "[Model] calibration"
- "[Model] sdk"

### 9. Stitching Solutions

**Real-time stitching is critical if we go omnidirectional route.**

**Search for:**
- [ ] Insta360 SDK stitching capabilities
- [ ] Open-source stitching for Insta360
- [ ] "insta360 stitching" + "realtime"
- [ ] "insta360 stitching" + "opencv"
- [ ] GPU-accelerated stitching for Insta360

**Document:**
- Stitching method (SDK vs custom)
- Performance (FPS, latency)
- Output format (equirectangular, cubemap, etc.)
- GPU acceleration
- Code availability

### 10. Calibration Data & Tools

**Search for:**
- [ ] Pre-calibrated camera parameters for Insta360 models
- [ ] Insta360 calibration tools/procedures
- [ ] Kalibr examples for Insta360
- [ ] OpenCV fisheye calibration for Insta360
- [ ] Dual fisheye calibration examples

**Document:**
- Available calibration files
- Calibration procedures
- Tools used
- Accuracy

---

## Investigation Steps

### Step 1: Broad GitHub Search (90 minutes) ⭐ PRIORITY
1. Go to https://github.com/search
2. Search with all keywords from Question 1
3. Apply filters:
   - Language: C++, Python
   - Sorted by: Stars, Recently updated, Best match
4. For each relevant repo (aim for 10-20 repos):
   - Star it for later
   - Document details in spreadsheet/notes
   - Clone promising ones for deeper inspection
   - Check if actively maintained
5. Look at "Used by" and "Forks" for derivatives

### Step 2: Academic Literature Search (90 minutes)
1. **Google Scholar**:
   - "Insta360" SLAM
   - "Insta360" "visual odometry"
   - "360 camera" SLAM drone
   - Date range: 2018-2024
2. **arXiv**:
   - cs.RO (Robotics)
   - cs.CV (Computer Vision)
   - Same keywords
3. **IEEE Xplore & ACM**:
   - ICRA, IROS, CVPR proceedings
   - "Insta360" OR "360 camera" OR "omnidirectional" + SLAM
4. Document all papers with:
   - Code availability
   - Dataset availability
   - Reproducibility potential

### Step 3: Insta360 Official Resources (45 minutes)
1. Visit Insta360 website: https://www.insta360.com/
2. Look for "Developers" or "SDK" section
3. Search: "Insta360 SDK download"
4. Check Insta360 GitHub: https://github.com/insta360 (if exists)
5. Look for official documentation
6. Check if SDK includes:
   - Camera API
   - Stitching API
   - IMU access
   - Linux support

### Step 4: ROS Ecosystem Search (45 minutes)
1. **ROS Index**: Search "insta360"
2. **GitHub**: "insta360_ros" OR "ros_insta360"
3. **ROS Wiki**: Search for Insta360 pages
4. **ROS Discourse**: Forum search
5. Check for:
   - Camera drivers
   - SLAM integrations
   - Launch files
   - Configuration examples

### Step 5: Community Forums Deep Dive (60 minutes)
1. **Reddit**: Search across robotics subreddits
2. **ROS Discourse**: Search and read threads
3. **Stack Overflow/Robotics SE**: Q&A about Insta360
4. **Chinese forums** (use Google Translate):
   - Zhihu: 知乎 搜索 "insta360 slam" or "insta360 导航"
   - CSDN: Search for blog posts
5. Note helpful community members (potential contacts)

### Step 6: YouTube & Video Content (30 minutes)
1. YouTube search with keywords
2. Sort by: Relevance, Upload date, View count
3. Watch relevant videos (at 1.5x speed)
4. Check video descriptions for code links
5. Note content creators (can we reach out?)

### Step 7: Company/Lab Project Pages (30 minutes)
1. Google: "insta360" site:*.edu
2. Google: "insta360 slam" site:github.io
3. Look for:
   - Lab project pages
   - Technical blog posts
   - Open-source releases
4. Check "Publications" pages of robotics labs

### Step 8: Specific Model Investigation (30 minutes)
1. Identify our exact Insta360 model (or most likely model for drones)
2. Targeted search for that specific model
3. Look for model-specific quirks or best practices
4. Find calibration data for that model

### Step 9: Stitching Pipeline Research (45 minutes)
1. Search for real-time stitching implementations
2. Check Insta360 SDK stitching performance
3. Look for GPU-accelerated stitching examples
4. Find open-source alternatives to SDK stitching
5. Benchmark data if available

### Step 10: Compile & Prioritize Findings (60 minutes)
1. Create a comprehensive list of all findings
2. Categorize by:
   - Ready to use
   - Needs minor modification
   - Reference/learning material only
   - Not relevant
3. Rank by potential value for our project
4. Identify top 3-5 candidates for deeper evaluation

---

## Deliverables

### 1. Executive Summary (1 page)
- **Key finding**: Did we find any ready-made or near-ready Insta360 SLAM solutions?
- **Best candidates**: Top 3-5 repositories/projects found
- **Quick wins**: Anything we can deploy immediately?
- **Learning resources**: Best references for understanding Insta360 SLAM

### 2. Comprehensive Repository List (2-3 pages)
Create a table with all repositories found:

| Repo Name | URL | Stars | Last Update | Insta360 Model | Algorithm | IMU? | Drone? | Dual/Stitched | Quality | Notes |
|-----------|-----|-------|-------------|----------------|-----------|------|--------|----------------|---------|-------|
| ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

### 3. Top Candidates Deep Dive (2-3 pages)
For top 3-5 candidates:
- **Repository**: Link and overview
- **What it does**: Detailed description
- **Our compatibility**:
  - Can we use it with our Insta360 model?
  - Does it support dual fisheye or do we need stitching?
  - IMU integration?
  - Ubuntu compatible?
- **Advantages**: Why this is promising
- **Challenges**: What needs to be modified/solved
- **Deployment estimate**: Time to get it working
- **Recommendation**: Should we pursue this?

### 4. Research Papers Summary (1-2 pages)
List all relevant papers found:
- Title, authors, year, venue
- Brief summary
- Code availability
- Relevance to our project
- Key insights or techniques we can borrow

### 5. Insta360 SDK Assessment (0.5-1 page)
- Does an SDK exist?
- What does it provide?
- Cost and licensing
- Linux support?
- Stitching capabilities
- IMU data access
- Should we use it?

### 6. ROS Integration Options (0.5-1 page)
- Available ROS packages for Insta360
- Quality and maintenance status
- What they provide (driver, SLAM, both?)
- Recommended package if any

### 7. Stitching Solutions (0.5 page)
- SDK stitching vs open-source
- Real-time performance expectations
- GPU acceleration options
- Recommended approach

### 8. Community Insights (0.5 page)
- Key forum discussions and takeaways
- Community members working on Insta360 SLAM
- Common challenges mentioned
- Recommended practices

### 9. Calibration Resources (0.5 page)
- Available calibration data for Insta360 models
- Calibration procedures found
- Tools and methods
- What we'll need to calibrate ourselves

### 10. Gap Analysis (0.5 page)
- What exists vs what we need
- What's missing that we'll have to build
- Risks and unknowns

### 11. Recommendation Matrix (1 page)
Create a decision matrix comparing:
- Ready-made Insta360 solutions (if found)
- vs. adapting existing SLAM systems (Stella, Basalt, VINS, etc.)

**Criteria**:
- Time to deployment
- Performance
- Reliability
- Maintenance burden
- Documentation quality
- Community support

### 12. Next Steps (1 page)
Based on findings:
- **If we found good Insta360 solutions**:
  - Which one(s) to test first?
  - Deployment plan
  - Timeline
- **If we didn't find suitable solutions**:
  - Confirm we should use general SLAM systems
  - Best path forward (Stella, Basalt, VINS?)
- **Hybrid approach**:
  - Can we combine Insta360-specific components (e.g., stitching) with general SLAM?

### 13. Complete Resource Links
Organized by category:
- GitHub repositories
- Academic papers
- SDK/official resources
- ROS packages
- Forum discussions
- Videos
- Contact information (researchers, developers)

---

## Success Criteria

Your research is successful if you can confidently answer:
1. ✅ Are there any Insta360-specific SLAM implementations we can use?
2. ✅ What is the most mature/ready Insta360 SLAM project?
3. ✅ Does Insta360 provide an SDK we should use?
4. ✅ Are there ROS packages for Insta360?
5. ✅ Has anyone done drone SLAM with Insta360?
6. ✅ What stitching solutions exist for real-time use?
7. ✅ Are there calibration resources available?
8. ✅ Should we use an Insta360-specific solution or adapt a general SLAM system?
9. ✅ What's the fastest path to a working system?

---

## Timeline

**Estimated research time**: 8-10 hours

**Suggested schedule**:
- Hours 1-2.5: GitHub repository search and documentation
- Hours 2.5-4: Academic paper search and reading
- Hours 4-5: Insta360 SDK and official resources
- Hour 5-6: ROS ecosystem investigation
- Hours 6-7: Community forums and discussions
- Hour 7-7.5: YouTube and video content
- Hour 7.5-8: Company/lab websites
- Hour 8-8.5: Stitching pipeline research
- Hours 8.5-10: Compilation, analysis, and report writing

---

## Notes

- **Priority**: VERY HIGH - This could save us weeks of work
- **Be Thorough**: Cast a wide net, don't miss hidden gems
- **Chinese Resources**: Don't ignore Chinese forums/repos (Insta360 is Chinese)
- **Version Differences**: Different Insta360 models may have very different software support
- **Contact People**: Note names of developers/researchers we might want to reach out to
- **Test Quickly**: If you find promising repos, try to build and test them quickly

---

## Search Keywords Master List

**GitHub/GitLab**:
- insta360 slam
- insta360 vio
- insta360 visual odometry
- insta360 navigation
- insta360 orb-slam / vins / basalt / stella
- insta360 drone / uav / quadcopter
- insta360 ros / ros2
- 360 camera slam
- omnidirectional slam insta360
- dual fisheye slam

**Academic**:
- "Insta360" SLAM
- "Insta360" "visual odometry"
- "360 degree camera" SLAM
- "omnidirectional camera" UAV
- "dual fisheye" drone

**General**:
- insta360 sdk
- insta360 developer
- insta360 api
- insta360 stitching
- insta360 calibration

---

## Expected Outcome

**Best case**: We find a mature, maintained Insta360 SLAM implementation that works out-of-the-box for our drone setup. This becomes our primary solution, saving weeks of integration work.

**Likely case**: We find several partial solutions, example code, and reference implementations that inform our approach. We adapt one of the mainstream SLAM systems (Stella, Basalt, VINS) but leverage Insta360-specific components (stitching, calibration) from these projects.

**Worst case**: We find very little Insta360-specific SLAM code, but learn valuable lessons about challenges and approaches from the community. We proceed with adapting a mainstream SLAM system from scratch.

**In all cases**: This research provides valuable intelligence that will inform our development approach and potentially save significant time.

---

Good luck! This could be the most valuable research of all - finding existing work means we don't reinvent the wheel! 🔍🎯
