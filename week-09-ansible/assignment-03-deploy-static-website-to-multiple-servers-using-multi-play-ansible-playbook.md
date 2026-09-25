# Assignment 03 — Deploy a Static Website to Multiple Servers Using a Multi-Play Ansible Playbook

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will create a multi-play Ansible playbook to install Nginx, deploy a static website to two Ubuntu servers, and verify that the website is accessible from both servers.

You may use either AWS EC2 instances or Azure Virtual Machines as your managed servers.

---

# Task 1 — Create the Project Structure

## Goal

Create the required folders and files for the Ansible project.

### Evidence

#### Screenshot 1 — Terminal or VS Code showing the complete `static-web` project structure

![Screenshot 1](<Screenshot 2026-09-24 130020.png>)

---

# Task 2 — Configure the Ansible Inventory

## Goal

Add both Ubuntu servers to the Ansible inventory.

### Evidence

#### Screenshot 2 — Output of `ansible-inventory -i inventory.ini --graph` showing `web1` and `web2`

![Screenshot 2](<Screenshot 2026-09-24 133737.png>)

---

### Configuration File

[web]
web1 ansible_host=16.192.160.116
web2 ansible_host=13.63.171.63

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/home/onyinw/.ssh/id_ed25519

# Task 3 — Verify Ansible Connectivity

## Goal

Confirm that the Ansible controller can connect to both servers.

## Evidence

### Screenshot 3 — Ansible ping output showing `SUCCESS` and `pong` for both servers

![Screenshot 3](<Screenshot 2026-09-24 134336.png>)

---

# Task 4 — Download and Personalize the Static Website

## Goal

Download `index.html` to the Ansible controller and personalize the website with your full name.

## Evidence

### Screenshot 4 — Edited `files/index.html` showing the footer line with your full name

![Screenshot 4](<Screenshot 2026-09-24 144516.png>)

---

# Task 5 — Create the Multi-Play Ansible Playbook

## Goal

Create a single Ansible playbook containing separate plays for installation, deployment, and verification.

## Configuration File

Copy and paste the complete contents of your `site.yml` file below:

---
- name: Install and configure Nginx
  hosts: web
  become: true
  tasks:
    - name: Update the APT package cache
      ansible.builtin.apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Start and enable Nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

- name: Deploy the static website
  hosts: web
  become: true
  tasks:
    - name: Copy index.html to the web root
      ansible.builtin.copy:
        src: files/index.html
        dest: /var/www/html/index.html
        owner: www-data
        group: www-data
        mode: "0644"
      notify: Reload nginx

  handlers:
    - name: Reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded

- name: Verify both websites from the controller
  hosts: localhost
  connection: local
  gather_facts: false
  become: false
  tasks:
    - name: Send an HTTP GET request to each web server
      ansible.builtin.uri:
        url: "http://{{ hostvars[item].ansible_host }}"
        status_code: 200
      loop: "{{ groups['web'] }}"
      register: website_checks

    - name: Confirm each server returned HTTP 200
      ansible.builtin.assert:
        that:
          - item.status == 200
        success_msg: "{{ item.item }} returned HTTP {{ item.status }}"
      loop: "{{ website_checks.results }}"
---

# Task 6 — Validate the Playbook Syntax

## Goal

Check the playbook for YAML or Ansible syntax errors before running it.

## Evidence

### Screenshot 5 — Successful syntax-check output showing `playbook: site.yml`

![Screenshot 5](<Screenshot 2026-09-24 151326.png>)


---

# Task 7 — Run the Multi-Play Playbook

## Goal

Install Nginx, deploy the website, and verify both servers in one playbook run.

## Evidence

### Screenshot 6 — Play 3 verification showing HTTP `200` for both servers
![Screenshot 6](<Screenshot 2026-09-24 151452.png>)
![Screenshot 6](<Screenshot 2026-09-24 151515.png>) 


---

### Screenshot 7 — Final play recap showing `unreachable=0` and `failed=0` for `web1`, `web2`, and `localhost`

![Screenshot 6](<Screenshot 2026-09-24 151601.png>) 


---

# Task 8 — Verify Idempotency

## Goal

Run the playbook again and confirm that it does not make unnecessary changes.

## Evidence

### Screenshot 8 — Second playbook run showing the play recap with `changed=0`, `unreachable=0`, and `failed=0` for both web servers

![Screenshot 8](<Screenshot 2026-09-24 152451.png>)

---

# Task 9 — Test Both Websites Manually

## Goal

Confirm that the static website is accessible from both public IP addresses.

## Evidence

### Screenshot 9 — `curl -I` output showing HTTP `200 OK` from both servers
![Screenshot 9](<Screenshot 2026-09-24 164048.png>)

---

### Screenshot 10 — Browser showing the website from Server 1 with the public IP and your full name visible

![Screesnhot 10](<Screenshot 2026-09-24 164308.png>)
![Screesnhot 10](<Screenshot 2026-09-24 164318.png>) 


---

### Screenshot 11 — Browser showing the website from Server 2 with the public IP and your full name visible
![Screenshot 11](<Screenshot 2026-09-24 164253.png>) 
![Screenshot 11](<Screenshot 2026-09-24 164243.png>)


---

## Website URLs

Add both deployed website URLs below:

```text
Server 1: http://16.192.160.116/
Server 2: http://13.63.171.63/
```

---

# Task 10 — Complete the Project README

## Goal

Document how the project works and record what you learned.

## README Content

Copy and paste the complete contents of your `README.md` file below:


## Project Overview
# Static Website Deployment with Ansible

This project uses Ansible to automate the deployment of a static website to two Ubuntu web servers running on AWS.

The Ansible playbook performs three main tasks:

1. Installs and configures Nginx on both web servers.
2. Deploys the static website to both servers.
3. Verifies that both websites are accessible and return HTTP 200.

The playbook is designed to be idempotent, meaning that running it again does not make unnecessary changes.

## Environment

* Cloud Provider: AWS
* Operating System: Ubuntu
* Web Server: Nginx
* Automation Tool: Ansible
* Controller: WSL2 Ubuntu
* Web Server 1: 16.192.160.116
* Web Server 2: 13.63.171.63

## Project Structure

```text
static-web/
├── inventory.ini
├── site.yml
├── files/
│   └── index.html
└── README.md
```

## How to Run

Activate the existing Ansible virtual environment:

```bash
source ~/ansible-onboarding/.venv/bin/activate
```

Navigate to the project directory:

```bash
cd ~/ansible-onboarding/static-web
```

Test connectivity to both web servers:

```bash
ansible web -i inventory.ini -m ping
```

Check the playbook syntax:

```bash
ansible-playbook -i inventory.ini site.yml --syntax-check
```

Run the playbook:

```bash
ansible-playbook -i inventory.ini site.yml
```

The playbook installs Nginx, deploys the website to both servers, reloads Nginx when the website changes, and verifies that both servers return HTTP 200.

## Issue Faced and Solution

During the syntax check, Ansible reported a YAML error because an extra `site.yml` line had been placed before the YAML document marker.

The issue was fixed by removing the extra line so that the playbook begins with:

```yaml
---
```

The syntax check then completed successfully with:

```text
playbook: site.yml
```

## What I Learned

I learned how to use a multi-play Ansible playbook to automate tasks across multiple servers.

I learned how to:

* Install and configure Nginx using Ansible.
* Deploy the same website to multiple servers.
* Use handlers to reload Nginx only when the website changes.
* Verify web servers using the Ansible `uri` module.
* Use the `assert` module to confirm HTTP 200 responses.
* Build an idempotent Ansible playbook.
* Separate installation, deployment, and verification into different plays.

## Why Installation and Deployment Are Separate

Installation and deployment are separated because they perform different jobs.

The first play prepares the servers by installing and starting Nginx.

The second play manages the website content.

Keeping these tasks separate makes the playbook easier to understand, maintain, troubleshoot, and reuse.

## Benefit of Ansible Copy Module

The Ansible `copy` module makes it easy to transfer and manage files on remote servers.

It also checks whether the file has changed. If the file is already correct, Ansible does not copy it again.

This supports idempotency and prevents unnecessary changes.

In this project, the `copy` module deploys the same `index.html` file to both web servers and triggers the Nginx reload handler only when the file changes.

## Verification

Both web servers were successfully verified.

* Web Server 1: HTTP 200 OK
* Web Server 2: HTTP 200 OK

The websites were also opened successfully in a browser and displayed the deployed static website.

A second Ansible playbook run produced:

```text
localhost : changed=0
web1      : changed=0
web2      : changed=0
```

This confirmed that the playbook is idempotent.


```

---

# LinkedIn Post Required

## Evidence

### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/nwoke-onyinye_ansible-aws-cloudengineering-ugcPost-7508917959659126784-w1LO/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c

---

### Screenshot — Published LinkedIn post

![LinkedIn Post](<Screenshot 2026-09-24 170218.png>)

---

# Assignment Questions

Answer the following in your own words:

**1. What issue did you face while completing this assignment, and how did you fix it?**

I initially had a YAML syntax error because an extra site.yml line was accidentally placed at the top of my playbook. I removed the extra line so the file started with ---. I then ran the syntax check again, and the playbook passed successfully.

---

**2. What did you learn from this assignment?**

I learned how to use a multi-play Ansible playbook to install Nginx, deploy a static website to multiple servers, and verify that the websites are working. I also learned how handlers and idempotency help Ansible avoid unnecessary changes.

---

**3. Why is it useful to split installation, deployment, and verification into separate plays?**

Splitting them makes the playbook easier to understand, maintain, and troubleshoot. Each play has one clear responsibility: prepare the servers, deploy the website, and then confirm that everything is working correctly.

---

**4. What is one benefit of using the Ansible `copy` module instead of cloning the website directly from Git on every managed server?**

The copy module allows me to keep one controlled copy of the website on the Ansible controller and deploy the same version to all servers. It also supports idempotency by only copying the file when it has changed.

---

**5. What does idempotency mean in this assignment?**



---

**6. What does the Ansible `uri` module verify in Play 3?**
Idempotency means I can run the same Ansible playbook multiple times and Ansible will not make unnecessary changes when the servers are already in the desired state. In my second run, both web servers showed changed=0.
---

# Required Files

Confirm that the following files are included in your assignment folder:

- [ ] `inventory.ini`
- [ ] `site.yml`
- [ ] `files/index.html`
- [ ] `README.md`

---

# Submission Instructions

- Add all required screenshots in the correct order.
- Full Name must be visible in required screenshots.
- Include both deployed website URLs.
- Paste `inventory.ini`, `site.yml`, and `README.md` as editable text.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, cloud account IDs, or other sensitive information.

---

# Completion Checklist

- [ ] Task 1: `static-web` folder structure is complete
- [ ] Task 2: Both servers are listed under the `[web]` group in `inventory.ini`
- [ ] Task 2: Inventory graph shows `web1` and `web2`
- [ ] Task 3: Ansible ping returns `SUCCESS` and `pong` for both servers
- [ ] Task 4: `files/index.html` contains your full name
- [ ] Task 5: `site.yml` contains three separate plays
- [ ] Task 5: Play 1 installs, starts, and enables Nginx
- [ ] Task 5: Play 2 deploys `index.html` using the `copy` module
- [ ] Task 5: Nginx reload handler is included
- [ ] Task 5: Play 3 verifies both web servers from the controller
- [ ] Task 6: Playbook syntax check passes
- [ ] Task 7: First playbook run completes with `unreachable=0` and `failed=0`
- [ ] Task 7: URI verification returns HTTP `200` for both servers
- [ ] Task 8: Second playbook run demonstrates idempotency
- [ ] Task 8: Second run shows `changed=0` for both web servers
- [ ] Task 9: Both `curl -I` commands return HTTP `200 OK`
- [ ] Task 9: Website loads from Server 1
- [ ] Task 9: Website loads from Server 2
- [ ] Task 9: Full name is visible on both deployed websites
- [ ] Task 10: `README.md` contains all required explanations
- [ ] Screenshots 1–11 are included
- [ ] `inventory.ini`, `site.yml`, and `README.md` are pasted as editable text
- [ ] Both website URLs are included
- [ ] Assignment questions are answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information is exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*