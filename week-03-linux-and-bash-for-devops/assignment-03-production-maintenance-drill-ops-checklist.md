# Assignment 3 — Production Maintenance Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will treat your already deployed React application (on Ubuntu VM with Nginx) as a live production system. You will perform structured operational checks covering network validation, service health, log analysis, resource monitoring, configuration verification, and incident simulation with recovery — mirroring real on-call DevOps responsibilities.

---

# Task 1 — Server Access & Networking Validation

## Goal

Verify that the deployed React application is reachable from the browser and confirm basic network connectivity of the Ubuntu VM.

### Evidence

#### Screenshot 1 — Browser showing the React app with your Full Name visible on the UI

![Screenshot 1](<Screenshot 2026-07-15 012335-1.png>)

---

#### Screenshot 2 — Output of `ip a`

![Screenshot 2](<Screenshot 2026-07-15 182330.png>)

---

#### Screenshot 3 — Output of `sudo ss -tulpen`

![Screenshot 3](<Screenshot 2026-07-15 182844.png>)

---

#### Screenshot 4 — Output of `sudo ufw status`

![Screenshot 4](<Screenshot 2026-07-15 183000.png>)

---

### Notes

Answer the following in your own words:

**1. What proves Nginx is listening on 0.0.0.0:80?**

The sudo ss -tulpen command showed Nginx listening on 0.0.0.0:80. This means Nginx is accepting HTTP connections on port 80 from any network interface.

---

**2. What proves SSH is active on port 22?**

The same ss output showed the SSH service listening on port 22, confirming that the server accepts SSH connections.

---

**3. Did you find any unexpected open ports? Explain briefly.**

No. Only ports 22 (SSH) and 80 (HTTP) were open, which are required for remote administration and serving the web application.

---

# Task 2 — Service Health & Systemd Validation (Nginx)

## Goal

Verify that Nginx is properly installed, running, enabled at boot, and safely configured.

### Evidence

#### Screenshot 1 — Output of `systemctl status nginx --no-pager`

![Screenshot 1](<Screenshot 2026-07-15 183804.png>)

---

#### Screenshot 2 — Output of `sudo nginx -t`

![Screenshot 2](<Screenshot 2026-07-15 183919.png>)

---

#### Screenshot 3 — Output of `sudo ss -lptn '( sport = :80 )'`

![Screenshot 3](<Screenshot 2026-07-15 184033.png>)

---

### Notes

Answer the following in your own words:

**1. What happens if Nginx fails to restart in production?**

Users will not be able to access the application because the web server is unavailable. This could result in downtime and impact business operations.

---

**2. What's your basic rollback plan?**

Restore the last known working Nginx configuration, test it using sudo nginx -t, then restart Nginx and verify that the application is accessible.

---

# Task 3 — Logs & Request Trace

## Goal

Verify real traffic flow and analyze logs to understand system behavior and errors.

### Evidence

#### Screenshot 1 — Output of `sudo tail -n 30 /var/log/nginx/access.log`

![Screenshot 1](<Screenshot 2026-07-15 184723.png>)

---

#### Screenshot 2 — Output of `sudo tail -n 30 /var/log/nginx/error.log`

![Screenshot 2](<Screenshot 2026-07-19 205251.png>)

---

#### Screenshot 3 — Output of `sudo journalctl -u nginx --no-pager -n 50`

![Screenshot 3](<Screenshot 2026-07-15 184823.png>).

---

### Notes

Answer the following in your own words:

**1. Were there any errors in the logs?**

- If yes, mention 1–2 example error lines from the logs and explain what each one means in simple terms.
- If no, explain what it means if the error log is empty or shows no recent errors during your check.

Write your answer here.

---

**2. If there were no errors, what does that indicate about the system?**

The error log was empty, which indicates Nginx did not encounter any recent problems while serving requests.

---

**3. Based on the access logs, were your curl requests visible in the log entries? What does that prove about traffic flow?**

It suggests the web server is operating normally and processing requests successfully.

---

# Task 4 — System Resource Health Check (Capacity Red Flags)

## Goal

Assess server capacity and detect potential performance or failure risks.

### Evidence

#### Screenshot 1 — Output of `uptime`

![Screenshot 1](<Screenshot 2026-07-15 185126.png>)

---

#### Screenshot 2 — Output of `free -h`

![Screenshot 2](<Screenshot 2026-07-15 185215.png>)

---

#### Screenshot 3 — Output of `df -h`

![Screenshot 3](<Screenshot 2026-07-17 183202.png>)

---

#### Screenshot 4 — Output of `sudo du -sh /var/* | sort -h`

![Screenshot 4](<Screenshot 2026-07-15 185327.png>)

---

### Notes

Answer the following in your own words:

**1. Which resource looks most critical right now? (CPU/load, memory, or disk) Explain why.**

None of the resources appeared critical. CPU load was low, memory usage was within acceptable limits, and there was sufficient free disk space.

---

**2. What happens if disk becomes 100% full in a production server?**

Applications may fail to write logs or data, services can crash, and the operating system may become unstable or unresponsive.

---

# Task 5 — Configuration & Deployment Verification

## Goal

Ensure the correct React build is deployed and Nginx is serving it properly.

### Evidence

#### Screenshot 1 — Output of `ls -lah /var/www/html | head -n 20`

![Screenshot 1](<Screenshot 2026-07-15 190037.png>)

---

#### Screenshot 2 — Output of `grep -R "Deployed by" -n /var/www/html 2>/dev/null | head`

![Screenshot 2](<Screenshot 2026-07-19 212030.png>)

---

#### Screenshot 3 — Output of `grep -n "try_files" /etc/nginx/sites-available/default`

![Screenshot 3](<Screenshot 2026-07-15 190353.png>)

---

### Notes

Answer the following in your own words:

**1. How do you confirm that the correct version of the application is deployed?**

I verified that the expected application files were present in /var/www/html and confirmed that my deployment identifier was found in the application files. I also verified that the Nginx configuration included the try_files directive required for React routing.

---

# Task 6 — Nginx Configuration Failure Simulation

## Goal

Simulate a real-world Nginx misconfiguration and recover the service safely.

### Evidence

#### Screenshot 1 — Output of `sudo nginx -t` showing the syntax error (broken config)

![Screenshot 1](<Screenshot 2026-07-15 190853-1.png>)

---

#### Screenshot 2 — Output of `sudo nginx -t` showing syntax ok (fixed config)

![Screenshot 2](<Screenshot 2026-07-15 183919-1.png>)

---

#### Screenshot 3 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

![Screenshot 3](<Screenshot 2026-07-15 191419.png>)

---

### Notes

Answer the following in your own words:

**1. What caused the configuration failure?**

A syntax error was introduced into the Nginx configuration file, preventing Nginx from validating the configuration.

---

**2. How did you fix the issue?**

I corrected the syntax error, verified the configuration using sudo nginx -t, and restarted Nginx.

---

**3. How can you avoid this kind of issue in real production systems?**

Always test configuration changes using nginx -t before restarting the service. Configuration changes should also be reviewed and tested in a staging environment before production deployment

---

# Task 7 — Web Application Failure Simulation

## Goal

Simulate missing deployment content and recover the application safely.

### Evidence

#### Screenshot 1 — Output of `curl -I http://<public-ip>` showing failure (non-200 response)

![Screenshot 1](<Screenshot 2026-07-15 191419-1.png>)

---

#### Screenshot 2 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

![Screenshot 2](<Screenshot 2026-07-15 191419-2.png>)

---

### Notes

Answer the following in your own words:

**1. What caused the application to break in this scenario?**

WThe deployed application files were removed from the web root, so Nginx had no content to serve.

---

**2. How did you fix the issue and restore the application?**

I restored the application files to /var/www/html and verified that the application was accessible again.
---

**3. What steps would you take to prevent this kind of issue in real production systems?**

Maintain deployment backups, automate deployments with CI/CD, and verify application health after every deployment to detect issues quickly

---

# Task 8 — Security & Reliability Review

## Goal

Review and reflect on the security and reliability practices applied during this assignment.

### Security & Reliability Notes

Answer the following in your own words:

**1. Why is SSH key-based authentication more secure than sharing passwords?**

SSH keys are significantly harder to guess or brute-force than passwords and provide stronger authentication without transmitting passwords over the network.

---

**2. Why should only required ports be open on a production server?**

Limiting open ports reduces the server's attack surface and minimizes opportunities for unauthorized access.

---

**3. Why is it important for Nginx to be enabled on boot?**

Enabling Nginx on boot ensures the web server starts automatically after a reboot, reducing downtime and maintaining application availability.

---

**4. What are the risks of sharing secrets, keys, or credentials publicly?**

Exposed credentials can allow attackers to gain unauthorized access to servers, cloud resources, applications, or sensitive data, potentially leading to security breaches.

---

**5. Why should cloud resources be stopped or terminated when they are no longer needed?**

Unused cloud resources continue to incur costs and can increase the security risk if left running. Cleaning them up helps reduce expenses and improves security.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/nwoke-onyinye_devops-aws-linux-share-7483283897670221824-Zu6h/?highlightedUpdateUrn=urn%3Ali%3Aactivity%3A7483283899024912384&highlightedUpdateType=SOCIAL_SHARE&origin=SOCIAL_SHARE&utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c

---

#### Screenshot — Published LinkedIn post

![LinkedIn Post](<Screenshot 2026-07-19 215836.png>)

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Task 1: Screenshots (browser, ip a, ss -tulpen, ufw status) + Notes answered
- [ ] Task 2: Screenshots (nginx status, nginx -t, ss port 80) + Notes answered
- [ ] Task 3: Screenshots (access log, error log, journalctl) + Notes answered
- [ ] Task 4: Screenshots (uptime, free -h, df -h, du -sh) + Notes answered
- [ ] Task 5: Screenshots (ls html, grep deployed by, grep try_files) + Notes answered
- [ ] Task 6: Screenshots (nginx -t fail, nginx -t pass, curl recovery) + Notes answered
- [ ] Task 7: Screenshots (curl failure, curl recovery) + Notes answered
- [ ] Task 8: Security & Reliability Notes answered
- [ ] LinkedIn post published and URL submitted
- [ ] Full Name visible in all required screenshots
- [ ] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*