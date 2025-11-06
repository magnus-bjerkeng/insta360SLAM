# Research Plan: Stella VSLAM for Insta360 Dual Fisheye Drone

## Mission Context

**Project**: Indoor drone SLAM with Insta360 dual fisheye camera + IMU
**Hardware**: GTX 5090, Ubuntu
**Camera**: Insta360 with two non-overlapping fisheye lenses (front & back)
**Sensors**: IMU available, extrinsic calibration known
**Requirements**: Minimum 1 FPS, preferably real-time (10-30 FPS)
**Approach for this system**: Stitch dual fisheye → equirectangular 360° image → SLAM

---

## Why Stella VSLAM?

Stella VSLAM (successor to OpenVSLAM) is identified as the **🏆 TOP CANDIDATE** for fastest deployment:
- **Native support for equirectangular (360°) images** - can directly process stitched Insta360 output
- **Demonstrated on Insta360 cameras** in research and projects
- **No image cropping or rectification required** for 360° mode
- **Fast deployment**: 1-2 weeks estimated time-to-working system
- **Limitation**: No native IMU integration (visual-only), but can add loose coupling externally

---

## Research Objectives

Your task is to conduct a **deep technical investigation** of Stella VSLAM to determine if it's the right choice for our Insta360 drone SLAM project. You will assess:
1. Technical feasibility on our hardware
2. Repository quality and community support
3. Setup complexity and documentation quality
4. Camera input options (dual fisheye vs omnidirectional)
5. Deployment options (Docker, pre-built binaries, etc.)
6. Insta360-specific implementations

---

## Critical Questions to Answer

### 1. Can We Run This on Our Machine?

**Investigate:**
- [ ] **OS Compatibility**: Does Stella VSLAM officially support Ubuntu? Which versions?
- [ ] **GPU Requirements**: Does it support NVIDIA GPUs? Can it leverage our GTX 5090?
- [ ] **Dependencies**: What are the system requirements? (OpenCV version, Eigen, DBoW2, etc.)
- [ ] **Build System**: CMake version, compiler requirements (C++17 confirmed, which GCC/Clang versions?)
- [ ] **Performance metrics**: Any published benchmarks showing FPS on similar hardware?
- [ ] **Memory requirements**: RAM usage during typical operation?

**Check for:**
- Compatibility with Ubuntu 20.04, 22.04, 24.04
- CUDA support for GPU acceleration (if available)
- Any known issues with modern GPUs

### 2. Repository Health & Community

**Investigate:**
- [ ] **GitHub stars**: How many? (Indicator of popularity)
- [ ] **Forks**: How many? (Indicator of active development/usage)
- [ ] **Last commit date**: Is the project actively maintained?
- [ ] **Contributors**: How many active contributors?
- [ ] **Releases**: Are there tagged releases? Semantic versioning?
- [ ] **License**: What license? (GPL, BSD, MIT, etc.)

**Main Repo**: https://github.com/stella-cv/stella_vslam
**Dense Reconstruction Fork**: https://github.com/RoblabWh/stella_vslam_dense

### 3. Open Issues Relevant to Our Use Case

**Search GitHub issues for:**
- [ ] "Insta360" - any issues specifically about Insta360 cameras?
- [ ] "fisheye" - issues related to fisheye camera support?
- [ ] "equirectangular" - issues with 360° image processing?
- [ ] "omnidirectional" - general omnidirectional camera issues?
- [ ] "performance" or "FPS" - any performance-related issues?
- [ ] "IMU" - any discussions about IMU integration (even if not native)?
- [ ] "Ubuntu" - any OS-specific issues?
- [ ] "CUDA" or "GPU" - GPU acceleration issues?

**Categorize issues:**
- How many open vs closed?
- Are issues being actively addressed by maintainers?
- Any showstopper bugs that would block our use case?

### 4. Documentation Quality

**Assess:**
- [ ] **README**: Is it comprehensive? Does it explain installation clearly?
- [ ] **Wiki/Docs site**: Is there a wiki or dedicated documentation site?
- [ ] **Installation guide**: Step-by-step instructions? Docker instructions?
- [ ] **Usage examples**: Are there example scripts/commands for running SLAM?
- [ ] **Configuration guide**: How to configure for equirectangular input?
- [ ] **Camera calibration**: Documentation on calibration process?
- [ ] **API documentation**: If we want to integrate, is there API docs?
- [ ] **Tutorials**: Any video tutorials or blog posts from the community?
- [ ] **Paper/Academic docs**: Is there a research paper explaining the system?

**Rate documentation on a scale**:
- Excellent: Comprehensive docs with examples
- Good: Sufficient docs to get started
- Fair: Basic docs, requires experimentation
- Poor: Minimal docs, difficult to use

### 5. Setup Complexity

**Investigate:**
- [ ] **Build process**: How many steps? Is it just `cmake && make`?
- [ ] **Dependencies**: How many? Are they easy to install via apt/pip?
- [ ] **Vocabulary file**: Does it need to download pre-trained vocabulary? How large?
- [ ] **Configuration files**: Are example configs provided? Easy to modify?
- [ ] **Camera calibration**: How complex is the calibration process?
- [ ] **Data input format**: What formats does it accept? (Video, image sequence, ROS bag?)
- [ ] **Output format**: What does it produce? (Trajectory file, map file, visualization?)

**Estimate setup time**:
- Simple: < 1 day
- Moderate: 1-3 days
- Complex: 1+ weeks

### 6. Dockerized Version

**Investigate:**
- [ ] **Official Docker image**: Does the repo provide a Dockerfile?
- [ ] **Docker Hub**: Is there a pre-built image on Docker Hub?
- [ ] **Docker documentation**: Is there a guide for Docker usage?
- [ ] **Community Docker images**: Have community members created Docker images?
- [ ] **Docker GPU support**: Does the Docker image support NVIDIA GPU passthrough?
- [ ] **Docker compose**: Is there a docker-compose.yml for easy deployment?

**Test if possible**:
- Can you pull and run a Docker image to verify it works?

### 7. Insta360-Specific Implementations

**Search for:**
- [ ] **Official Insta360 examples**: Does the repo have examples with Insta360 data?
- [ ] **Community projects**: Search GitHub for "stella vslam insta360" or "openvslam insta360"
- [ ] **Research papers**: Any academic papers using Stella VSLAM with Insta360?
- [ ] **Blog posts/tutorials**: Community tutorials using Insta360 + Stella VSLAM?
- [ ] **Insta360 SDK integration**: Has anyone integrated the Insta360 SDK for live input?
- [ ] **Stitching pipelines**: Examples of real-time stitching → Stella VSLAM pipelines?

**Search resources:**
- GitHub (search code and issues)
- Google Scholar
- arXiv
- YouTube tutorials
- Reddit (r/computervision, r/robotics)

### 8. Camera Input Options: Dual Fisheye vs Omnidirectional

**Investigate:**
- [ ] **Native support**: What camera models are natively supported?
  - Perspective
  - Fisheye (which model? Kannala-Brandt, EUCM, etc.)
  - Equirectangular
- [ ] **Dual fisheye option**: Can Stella VSLAM work with two separate fisheye cameras?
  - As stereo fisheye?
  - As independent monocular instances?
- [ ] **Omnidirectional/equirectangular**: Confirmed support for 360° stitched images?
- [ ] **Preprocessing required**: For Insta360 dual fisheye, do we MUST stitch first?
- [ ] **Stitching recommendations**: What stitching method is recommended?
  - Insta360 SDK?
  - OpenCV Stitcher?
  - Custom stitching?
- [ ] **Resolution recommendations**: What resolution for equirectangular input? (2K, 4K, 8K?)

**Determine our best approach**:
- Option A: Stitch dual fisheye → 4K equirectangular → Stella VSLAM
- Option B: Single fisheye (front or back) → Stella VSLAM
- Option C: Not feasible with our camera setup

---

## Investigation Steps

### Step 1: Repository Analysis (30 minutes)
1. Clone the repository: `git clone https://github.com/stella-cv/stella_vslam`
2. Examine the file structure
3. Read the main README.md thoroughly
4. Check for docs/ or wiki/
5. Look at CMakeLists.txt to understand dependencies
6. Check GitHub insights (stars, forks, activity)

### Step 2: Issue Analysis (30 minutes)
1. Go to GitHub Issues tab
2. Use search filters for keywords (listed in Question 3 above)
3. Sort by "Most commented" to find important issues
4. Check closed issues for solved problems relevant to us
5. Note any blockers or major concerns

### Step 3: Documentation Review (45 minutes)
1. Read installation docs in detail
2. Look for camera model documentation
3. Find example config files (especially for equirectangular)
4. Search for calibration guides
5. Look for API/integration documentation if available
6. Watch any video tutorials linked in README

### Step 4: Docker Investigation (20 minutes)
1. Check if repo has Dockerfile or docker/ directory
2. Search Docker Hub: https://hub.docker.com/search?q=stella%20vslam
3. Search GitHub for "stella vslam docker" to find community images
4. Read any Docker-specific documentation

### Step 5: Community Search for Insta360 (45 minutes)
1. **GitHub code search**: `stella vslam insta360` and `openvslam insta360`
2. **GitHub issues search**: Same keywords across all repos
3. **Google Scholar**: "Stella VSLAM" OR "OpenVSLAM" AND "Insta360"
4. **arXiv**: Same search
5. **YouTube**: "stella vslam insta360" or "openvslam 360 camera"
6. **Reddit**: Search r/computervision and r/robotics
7. **ROS Discourse**: Check for ROS integration discussions

### Step 6: Camera Model Investigation (30 minutes)
1. Find the camera model configuration files
2. Determine what models are supported (look in src or include directories)
3. Check if there are example configs for:
   - Fisheye cameras
   - Equirectangular/omnidirectional
4. Look for stitching documentation or recommendations
5. Check if anyone has documented Insta360 stitching → Stella pipeline

### Step 7: Dense Reconstruction Fork (20 minutes)
Investigate the specialized fork: https://github.com/RoblabWh/stella_vslam_dense
1. What additional features does it provide?
2. GPU acceleration for dense reconstruction?
3. Is it actively maintained?
4. Does it have better/worse documentation than main Stella?
5. Any Insta360-specific mentions?

---

## Deliverables

Please provide a **detailed report** with the following sections:

### 1. Executive Summary (2-3 paragraphs)
- Overall assessment: Is Stella VSLAM a good fit for our Insta360 drone SLAM project?
- Key strengths and weaknesses
- Go/No-Go recommendation with confidence level (High/Medium/Low)

### 2. Technical Feasibility (1 page)
- Hardware compatibility (Ubuntu + GTX 5090)
- Expected performance (FPS estimate)
- Dependencies and build complexity
- Estimated setup time

### 3. Repository Health (0.5 page)
- Stars, forks, activity level
- Maintenance status
- Community size and engagement

### 4. Critical Issues (0.5-1 page)
- List of relevant open issues (with links)
- Any showstoppers or major concerns
- Community responsiveness to issues

### 5. Documentation Assessment (0.5 page)
- Quality rating (Excellent/Good/Fair/Poor)
- What's well documented vs. what's missing
- Availability of tutorials and examples

### 6. Deployment Options (0.5 page)
- Docker availability and quality
- Build-from-source complexity
- Pre-built binaries (if any)

### 7. Insta360 Compatibility (1 page) ⭐ CRITICAL
- Evidence of Insta360 usage in the wild
- Links to example projects/papers/tutorials
- Recommended stitching approach
- Dual fisheye vs omnidirectional: which path to take?

### 8. Camera Input Analysis (0.5 page)
- Supported camera models
- Best approach for our Insta360 setup
- Configuration requirements

### 9. Comparison with Dense Fork (0.5 page)
- Should we use main Stella VSLAM or the dense reconstruction fork?
- Pros/cons of each

### 10. Next Steps (0.5 page)
If we proceed with Stella VSLAM:
- Step-by-step deployment plan
- What to prepare (calibration, stitching pipeline, etc.)
- Estimated timeline to working system
- Potential risks and mitigation strategies

### 11. Resource Links
- All relevant GitHub repos, issues, papers, tutorials found
- Organized by category

---

## Success Criteria

Your research is successful if you can confidently answer:
1. ✅ Can we run Stella VSLAM on Ubuntu with GTX 5090?
2. ✅ Is the repository healthy and actively maintained?
3. ✅ Are there any showstopper issues?
4. ✅ Is documentation sufficient to get started?
5. ✅ How complex is the setup process?
6. ✅ Is there a Docker option to simplify deployment?
7. ✅ Has anyone used Stella VSLAM with Insta360 before?
8. ✅ Should we use dual fisheye or stitch to omnidirectional?
9. ✅ Overall: Should we invest time in deploying Stella VSLAM?

---

## Timeline

**Estimated research time**: 4-6 hours for thorough investigation

**Suggested schedule**:
- Hour 1: Repository + documentation review
- Hour 2: Issue analysis + community health
- Hour 3: Insta360-specific research (GitHub, papers, tutorials)
- Hour 4: Camera model analysis + Docker investigation
- Hour 5-6: Report writing with recommendations

---

## Notes

- **Priority**: This is a HIGH PRIORITY investigation since Stella VSLAM is our top candidate for fastest deployment
- **Approach**: Be thorough but practical - focus on information that helps us make a deployment decision
- **Evidence**: Provide links and specific examples, not just opinions
- **Recommendation**: End with a clear Go/No-Go/Conditional recommendation

---

## Key GitHub Repositories

- **Main Stella VSLAM**: https://github.com/stella-cv/stella_vslam
- **Dense Reconstruction Fork**: https://github.com/RoblabWh/stella_vslam_dense
- **Original OpenVSLAM** (archived): https://github.com/xdspacelab/openvslam

Good luck with your investigation! 🚀
