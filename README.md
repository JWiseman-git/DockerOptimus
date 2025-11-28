# DockerOptimus
Lightning fast docker file optimisations 

## Why build this? 

- General annoyance at LLMs producing inefficient docker files.
- Inconsistency at how LLMs approach the problem of interating the process of optimising docker files. 

## Inefficiencies Commonly Found in AI-Generated Dockerfiles

AI-generated Dockerfiles are usually functional, but they often exhibit inefficiencies related to image size, build speed, security, and best-practice adherence. These issues arise from how large language models (LLMs) are trained and what they optimise for.

## 1. Common Inefficiencies

### 1.1. Use of Bloated Base Images
LLMs often select large base images such as `python:3.10` rather than leaner alternatives like `python:3.10-slim` or Alpine-based variants.  
**Impact:** Significantly larger image sizes.

### 1.2. Missing Multi-Stage Builds
AI models frequently produce single-stage Dockerfiles even when multi-stage patterns are standard (e.g., Go, Rust, Node.js).  
**Impact:** Larger, slower, and less secure final images.

### 1.3. Poor Caching Structure
A common pattern is copying all source code and then installing dependencies:
```
COPY . .
RUN pip install -r requirements.txt
```
This breaks caching when any file changes.  
**Impact:** Slower builds and inefficient cache usage.

### 1.4. Installing Unnecessary Build Tools
LLMs often include broad package installations (e.g., `build-essential`) even if the application does not strictly need them.  
**Impact:** Bloat and potential security surface expansion.

### 1.5. Inefficient Layering
Examples include multiple `RUN` instructions instead of consolidating them, failing to clean package caches, and leaving temporary artefacts behind.  
**Impact:** Larger layers and slower builds.

### 1.6. Security Oversights
Typical omissions include:

- Not dropping privileges (`USER app`)
- Using unpinned versions or `latest`
- Leaving unused packages in the final image
- Copying the entire build context instead of selective paths

**Impact:** Increased vulnerability exposure.

---

## 2. Why These Inefficiencies Occur

### 2.1. Pattern Mimicry Over Optimisation
LLMs generate text that looks correct but have no awareness of runtime properties such as image size or build performance.

### 2.2. Training Data Bias
Public tutorials and basic examples dominate training data, leading models to reproduce beginner-level patterns rather than production-grade ones.

### 2.3. Safety Bias Toward Completeness
AI systems tend to “over-install” dependencies to avoid producing non-working builds. Missing a dependency feels more like an error than adding unnecessary packages.

### 2.4. No Feedback Loop
LLMs do not observe Docker build output. Without metrics such as build time or image size, they cannot learn to optimise for them.

---

## 3. When AI-Generated Dockerfiles *Are* Efficient
With explicit prompting (e.g., “create a small, production-ready multi-stage Dockerfile”), some LLMs can generate highly optimised results, including:

- slim base images  
- proper caching structure  
- multi-stage builds  
- non-root execution  
- reduced layers  

However, these optimisations typically require **explicit instruction** and are not the default.


