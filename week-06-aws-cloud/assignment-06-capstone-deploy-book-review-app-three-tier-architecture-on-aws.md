# Assignment 6 — Capstone: Deploy Book Review App (Three-Tier Architecture) on AWS

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

This is the most important assignment of the course. You will deploy the Book Review App in a fully production-style three-tier architecture on AWS: a Next.js Web Tier behind Nginx and a public ALB, a private Node.js/Express App Tier behind an internal ALB, and a private Multi-AZ MySQL RDS database with a read replica. You are expected to design, deploy, isolate, debug, and document the result independently.

---

# Task 1 — Architecture Diagram

## Goal

Create an architecture diagram showing the custom VPC (10.0.0.0/16), the six subnets across two Availability Zones (two public Web Tier, two private App Tier, two private Database Tier), the public ALB, Web Tier EC2/Nginx, internal ALB, private App Tier EC2, private Multi-AZ RDS with its read replica, and the permitted traffic flow.

### Evidence

#### Diagram image or link

![Screenshot 1](<Screenshot 2026-08-20 221201.png>)

---

# Task 2 — AWS Region & Services Used

## Goal

Record the AWS Region used and list every AWS service used across networking, compute, load balancing, security, and the database.

### Notes

**Region:**

Write your answer here.

eu-north-1 (Stockholm)

**Services used:**

Write your answer here.

Amazon VPC
Amazon EC2
Application Load Balancers
Auto Scaling
Amazon RDS for MySQL
Amazon CloudWatch
IAM
Security Groups
Target Groups
Internet Gateway
NAT Gateway
Route Tables

# Task 3 — Public Entry Point

## Goal

Confirm the Book Review App loads through the public ALB DNS name.

### Evidence

#### Public ALB DNS

Paste your public ALB DNS name here:

http://book-review-web-alb-97783939.eu-north-1.elb.amazonaws.com/

---

# Task 4 — Evidence Screenshots

## Goal

Capture visual proof of every tier and load balancer.

### Evidence

#### Screenshot 1 — Web Tier EC2 instance in a public subnet

![Screenshot 1](<Screenshot 2026-08-25 130612.png>)

---

#### Screenshot 2 — App Tier EC2 instance in a private subnet

![Screenshot 2](<Screenshot 2026-08-25 130636.png>)

---

#### Screenshot 3 — Public Application Load Balancer configuration or healthy targets

![Screenshot 3](<Screenshot 2026-08-25 132238.png>)

---

#### Screenshot 4 — Internal Application Load Balancer configuration or healthy targets

![Screenshot 4](<Screenshot 2026-08-25 132809.png>)

---

#### Screenshot 5 — Amazon RDS for MySQL showing Multi-AZ and the read replica

![Screenshot 5](<Screenshot 2026-08-25 135826.png>)

---

#### Screenshot 6 — Book Review App UI working through the public ALB

![Screenshot 6](<Screenshot 2026-08-26 175957.png>)

---

# Task 5 — Summary

## Goal

Summarize what worked in the final deployment, the issues encountered and how each was fixed, and the tools or sources used to research and debug.

### Notes

**What worked:**

Successfully deployed a highly available Book Review application on AWS using a multi-tier architecture.

The final deployment includes:

A Public Application Load Balancer receiving internet traffic.
A Web EC2 instance running Nginx and the Next.js frontend.
An Internal Application Load Balancer routing API requests to the application tier.
An App EC2 instance running the Node.js backend on port 3001.
Amazon RDS MySQL for persistent application data.
PM2 to manage the frontend and backend Node.js processes and automatically restart them after system reboots.
Nginx configured as a reverse proxy, routing frontend traffic to Next.js and /api requests to the internal Application Load Balancer.
Health checks configured through the target groups.

The final connectivity tests were successful and the application was accessible through the Web EC2 public IP and through the Public ALB.
---

**Issues encountered and fixes:**

MySQL client was not installed: When attempting to connect to RDS from the App EC2, the command returned (Command 'mysql' not found). To fix, the MySQL client needed to be installed on the App EC2 before using the mysql command.

SSH private key location problem: The private key HA-KP.pem was stored on the local Windows machine, but SSH commands were sometimes executed from inside an EC2 instance. This resulted in errors such as "Identity file /home/ubuntu/Downloads/HA-KP.pem not accessible". To fix: SSH commands were run from the local Windows terminal using the correct Windows path:ssh -i "C:\Users\onyin\Downloads\HA-KP.pem" ubuntu@PUBLIC_IP

---

**Tools/sources used:**

The following tools and sources were used during the deployment, configuration and troubleshooting:

AWS Management Console for EC2, VPC, Security Groups, Target Groups, Application Load Balancers, RDS and networking configuration.
AWS EC2 system logs and instance information for troubleshooting instance connectivity and configuration.
AWS Target Group health checks to verify that the Web and App EC2 instances were reachable and responding on the expected ports.
Linux/Ubuntu terminal for server administration and troubleshooting.
SSH for secure remote access to EC2 instances.
Nginx for reverse proxy and web traffic routing.
PM2 for Node.js process management and automatic startup.
Node.js and npm for running and building the application.
Git and GitHub for source code management and repository deployment.
curl for testing frontend and backend HTTP endpoints.
systemctl for checking and managing Nginx and PM2 system services.
OpenSSL for generating the JWT secret.
MySQL client for testing connectivity to Amazon RDS.
Application and PM2 logs for identifying backend startup and port-binding errors.
Next.js build output for verifying that the frontend production build completed successfully.

---

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post sharing the capstone deployment, including the public ALB DNS (or a redacted screenshot), three to five lines on what you built and why it is production-style, and one proof screenshot.

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/nwoke-onyinye_aws-cloudcomputing-devops-activity-7498438221786161152-fq_q?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c

---

#### Screenshot — Published LinkedIn post

![linkedIn Post](<Screenshot 2026-08-26 185650.png>)

---

# Submission Instructions

- Add all required screenshots and links in your submission
- Do not expose passwords, RDS credentials, connection strings, private keys, or account IDs

---

# Completion Checklist

- [ ] Task 1: Architecture diagram completed
- [ ] Task 2: AWS Region and services documented
- [ ] Task 3: Public ALB DNS confirmed working
- [ ] Task 4: All six evidence screenshots captured (Web Tier, App Tier, both ALBs, RDS + replica, app UI)
- [ ] Task 5: Deployment summary completed (what worked, issues/fixes, tools/sources)
- [ ] LinkedIn post published and URL submitted
- [ ] App Tier and Database Tier confirmed not publicly accessible
- [ ] No sensitive data exposed

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
