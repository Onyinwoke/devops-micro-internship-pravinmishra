# Assignment 2 — Multi-Stage Docker Build for a React Application

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build both a single-stage and an optimized multi-stage Docker image for a React application, compare the resulting image sizes, and deploy the optimized version using a production-ready Nginx runtime container.

---

# Task 1 — Prepare the Project

## Goal

Clone `https://github.com/pravinmishraaws/my-react-app.git` and create a `.dockerignore` excluding `node_modules`, `build`, and `.env`.

### Evidence

#### Screenshot 1 — Contents of the `.dockerignore` file

![Screenshot 1](<Screenshot 2026-10-26 124306.png>)

---

# Task 2 — Create a Single-Stage Docker Image

## Goal

Create `Dockerfile.single`, build `react-single`, and run it on port 3000.

### Evidence

#### Screenshot 2 — Contents of `Dockerfile.single`

![Screenshot 2](<Screenshot 2026-10-26 234743.png>)

---

#### Screenshot 3 — Browser displaying the application running from the single-stage container

![Screenshot 3](<Screenshot 2026-10-26 140511.png>)

---

# Task 3 — Create a Multi-Stage Docker Build

## Goal

Create a multi-stage Dockerfile with separate build and Nginx runtime stages, build `react-multistage`, and run it on port 80.

### Evidence

#### Screenshot 4 — Contents of the multi-stage Dockerfile

![Screenshot 4](<Screenshot 2026-10-26 234946.png>)

---

#### Screenshot 5 — Browser displaying the application running from the multi-stage container

![Screenshot 5](<Screenshot 2026-10-26 140511-1.png>)

---

# Task 4 — Compare Docker Image Sizes

## Goal

Compare the single-stage and multi-stage image sizes and calculate the percentage reduction.

### Evidence

#### Screenshot 6 — Docker image list showing both image sizes

![Screenshot 6](<Screenshot 2026-10-26 235354.png>)

Using the reported disk usage:
Single-stage image: 2,170 MB (approximately)
Multi-stage image: 95.1 MB
Space saved: 2,170 − 95.1 = 2,074.9 MB
The approximate percentage reduction is:

{2170-95.1}{2170}\x100
95.6%

---

# Task 5 — Analyze the Optimization Results

## Goal

Write a 5–8 line analysis covering the percentage reduction, security benefits, reduced attack surface, faster distribution, and one build-caching optimization used.

### Evidence

#### Screenshot 7 — Analysis included in your submission document

![Screenshot 7](<Screenshot 2026-10-27 005413.png>)

---

### Notes

Write your analysis here.

The single-stage Docker image (`react-app:single`) had a content size of 505 MB, while the multi-stage image (`react-app:multistage`) was 26.6 MB, representing a **94.7% reduction** in image size. Multi-stage builds keep the Node.js build environment and unnecessary development tools out of the final production image. This improves security by reducing the number of packages that could contain vulnerabilities. Fewer installed tools and dependencies also reduce the container's attack surface. Smaller images require less data to transfer from a container registry, improving image pull times and potentially speeding up deployments. For build caching, I copied `package*.json` before the application source code and ran `npm ci` first, allowing Docker to reuse the dependency-installation layer when only source files change.

---

# Task 6 — Explore Additional Production Optimizations (Optional)

## Goal

Optionally configure an Nginx health check, cache headers, parameterized ports via environment variables, or a lighter runtime image, and compare results.

> Screenshot optional.

---

# LinkedIn Post (Optional)

## Goal

Create a LinkedIn post describing what you built, what a multi-stage Docker build is, the image size reduction achieved, and key learnings.

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`https://www.linkedin.com/posts/nwoke-onyinye_docker-devops-cloudengineering-ugcPost-7520637072546877440-RaUD/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c`

---

#### Screenshot — Published LinkedIn post

![LinkedIn Post](<Screenshot 2026-10-27 010742.png>)

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information

---

# Completion Checklist

- [ ] Task 1: `.dockerignore` created (Screenshot 1)
- [ ] Task 2: Single-stage image built and verified (Screenshots 2–3)
- [ ] Task 3: Multi-stage image built and verified (Screenshots 4–5)
- [ ] Task 4: Image sizes compared (Screenshot 6)
- [ ] Task 5: Analysis written (Screenshot 7 & Notes)
- [ ] Task 6: Optional production optimizations explored
- [ ] No sensitive information exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
