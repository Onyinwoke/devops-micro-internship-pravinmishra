# Week 00 - Internet and Networking

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

# 🧑‍💻 Task 1: Using ChatGPT as Your Learning Assistant

## Scenario

You're new to DevOps and will frequently encounter technical questions. ChatGPT can be your learning companion.

## Your Task

Write a clear ChatGPT prompt to help you understand:

> "What is a protocol in networking? Explain with a simple real-life example."

Take a screenshot of your interaction showing:

* Your detailed prompt (with clear expectations)
* ChatGPT's simplified response with an example

## Screenshot

Save your screenshot in the `screenshots` folder and update the file name below.

![Task 1 Screenshot](screenshots/task-1-chatgpt.png)


Replace `task-1-chatgpt.png` with your actual screenshot file name.

---

## What I Learned (2–3 lines)

A protocol is a set of rules that devices follow to communicate with each other over a network. The real-life example helped me understand that, just like people need common rules to communicate, computers also need protocols to exchange information correctly.

---

# 🌐 Task 2: Internet and Networking

## Scenario

Your friend is launching an online bookstore named **EpicReads**.

He asked you to explain how users globally can access his website hosted in Finland.

## Your Task

Write a short explanation (**100–150 words**) that includes:

* Packet Switching
* IP Address
* TCP/IP
* HTTP/HTTPS

💡 **Tip:** You may use ChatGPT (as demonstrated in Task 1) to refine your explanation.

## Answer

When a user in any part of the world visits the EpicReads website hosted in Finland, the information is divided into small pieces called packets. Packet switching allows these packets to travel across different networks and routes before reaching the destination. The website server has an IP address that identifies its location on the internet. TCP/IP provides the rules that allow the user's device and the server to communicate and ensure data is delivered correctly. HTTP or HTTPS is used to transfer web pages and other website content between the user's browser and the server. HTTPS is preferred because it encrypts the communication, helping protect users' information while they browse and purchase books from EpicReads.

---

# 🏗️ Task 3: Application Architecture & Stack

## Scenario

EpicReads bookstore has two application versions:

### Two-Tier Application

* FrontEnd 
* Backend

### Three-Tier Application

* FrontEnd
* Backend
* Database

## Your Task

* Draw simple diagrams (hand-drawn or tool-based such as draw.io)
* Label each layer clearly
* List at least two common technologies or tools used for each layer
* Submit a screenshot or photo clearly showing your own drawing

## Diagram Screenshot / Photo

Save your diagram image in the `screenshots` folder and update the file name below.

![Two Tier Architecture Diagram](<Screenshot 2026-07-17 163648.png>)

![Three Tier Architecture Diagram](<Screenshot 2026-07-17 163701.png>)


Replace `task-3-diagram.png` with your actual diagram file name.

---

## Technologies Used

### Frontend

* React
* HTML/CSS

### Backend

* Node.js
* Express.js

### Database

* MySQL
* PostgreSQL

---

# 🌍 Task 4: Domain Name & DNS (Basic Concepts)

## Scenario

Your friend's bookstore **EpicReads** is currently accessible through:

```text
52.172.142.222:3000
```

He purchased the domain:

```text
epicreads.com
```

## Your Task

In **50–100 words**, explain in your own words:

1. What is DNS (Domain Name System)?
2. Which DNS record type should be used to connect the domain to the given IP, and why?

## Answer

DNS, or Domain Name System, is like the internet's phonebook. It translates an easy-to-remember domain name such as epicreads.com into the server's IP address, 52.172.142.222, so users can access the website without remembering the numerical IP address. An A record should be used because it connects a domain name directly to an IPv4 address. Therefore, the A record would point epicreads.com to 52.172.142.222, allowing users to reach the EpicReads website by entering the domain name in their browser.

---

# 💻 Task 5: Visual Studio Code Setup (Hands-on)

## Your Task

Install Visual Studio Code (if not already installed).

Take a screenshot of your VS Code environment showing:

* Terminal open inside VS Code
* Running a basic command:

### Windows

```powershell
dir
```

### Linux / macOS

```bash
pwd
ls
```

* Your selected VS Code theme clearly visible

⚠️ **Important:** The screenshot must show your username or another identifiable detail to confirm it is your environment.

## Screenshot

Save your screenshot in the `screenshots` folder and update the file name below.

![Powershell Command Screenshot](<Screenshot 2026-07-17 164908.png>)

![Bash Command Screenshot](<Screenshot 2026-07-17 165058.png>)



Replace `task-5-vscode.png` with your actual screenshot file name.

---

# 🔗 Task 6: Publish Your Assignment as a LinkedIn Post

## Objective

Publishing on LinkedIn helps you:

* Build your professional online presence
* Reinforce your learning
* Document your DevOps journey publicly

## Your Task

Summarize your answers from Tasks 1–5 into a LinkedIn post.

Clearly structure your post into the following sections:

* ChatGPT
* Internet & Networking
* App Architecture
* DNS
* VS Code Setup

Add the following credit note at the end of your post:

> **P.S. This post is part of the DevOps Micro Internship (DMI) with Agentic AI — Cohort 3 — by Pravin Mishra. My graded progress is public: https://dmi.pravinmishra.com/s/YOUR-GITHUB-USERNAME.html · Start your DevOps journey: https://dmi.pravinmishra.com/?utm_source=student&utm_medium=ps-linkedin&utm_campaign=cohort3**

---

## LinkedIn Post URL

Paste your LinkedIn post URL here:

```text
https://www.linkedin.com/posts/nwoke-onyinye_devops-cloudengineering-networking-share-7440083379083010049-cVXi/?highlightedUpdateUrn=urn%3Ali%3Aactivity%3A7440083382333739008&highlightedUpdateType=SOCIAL_SHARE&origin=SOCIAL_SHARE&utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c
```

---

## LinkedIn Post Backup Copy

Paste the full text of your LinkedIn post here:

Starting my DevOps journey with Pravin Mishra — Week 0 reflections.
One thing is already clear: these basics are not optional. They are the foundation for everything in DevOps. Without understanding them properly, it becomes difficult to work with real systems, troubleshoot issues, or scale applications.
Here is a quick breakdown of what I learned:
🔹 Networking Basics
Protocols as the rules that make communication between systems possible.
🔹 Core Internet Concepts
• Packet Switching – how data moves across networks
• IP Address – how systems are identified
• TCP/IP – ensures reliable delivery
• HTTP/HTTPS – how browsers and servers communicate securely
🔹 Application Architecture
• Two-tier architecture (client + server)
• Three-tier architecture (client + application + database)
Plus the tools used at each layer.
🔹 DNS Fundamentals
How domain names map to IP addresses and how an A record connects epicreads.com to a server.
#DevOps #CloudEngineering #Networking #LearningInPublic #TechCommunity #AWS #Linux
P.S. This post is part of the DevOps Micro Internship (DMI) with Agentic AI — Cohort 3 — by Pravin Mishra. My graded progress is public: https://github.com/Onyinwoke/devops-micro-internship-pravinmishra.git
 · Start your DevOps journey: https://dmi.pravinmishra.com/?utm_source=student&utm_medium=ps-linkedin&utm_campaign=cohort3

---

# Reflection – Week 0

### What did you find easy?

I found it easy to understand the fundamental networking concepts such as protocols, IP addresses, DNS, and HTTP/HTTPS because they could be related to everyday activities like sending mail, making phone calls, and browsing websites. Using practical examples such as the EpicReads bookstore helped me visualize how these concepts work together to deliver a website to users.

---

### What was difficult?

The most challenging part was understanding how all the networking components work together behind the scenes. Concepts like the TLS handshake, different API types, HTTP methods, and how DNS records, application architecture, and the CI/CD pipeline interact required a deeper understanding. However, breaking each topic into simple examples made it much easier to connect the dots and see how modern web applications are built and delivered.

---

### What will you improve next week?

I want to improve my understanding of how networking concepts are applied in real-world DevOps environments. While I now understand the theory behind protocols, DNS, APIs, TLS, and CI/CD, I would like to gain more hands-on experience by configuring servers, troubleshooting network issues, and building projects that use these technologies. Practicing with tools such as Git, Docker, Kubernetes, and cloud platforms will help strengthen my practical skills and prepare me for a career in DevOps.

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.


## 📌 Resources

- 🌐 **DMI Official Website:** https://pravinmishra.com/dmi  
- 🎓 **DevOps for Beginners (Udemy):** https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 **Ultimate Agentic AI DevOps with Clude Code** https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/?referralCode=448389767BC96284087B
- 🎓 **DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm** https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/?referralCode=1C5B734505D65A010FA3
- ▶️ **YouTube Playlist (DMI Cohort 3):** https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 **Pravin Mishra (LinkedIn):** https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 **CloudAdvisory (LinkedIn):** https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track*