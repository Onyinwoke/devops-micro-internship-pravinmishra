# Assignment 02 — Provision Linux VMs with Terraform and Run Ansible Ad-Hoc Commands

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision three or four Ubuntu Linux Virtual Machines on either Microsoft Azure or Amazon Web Services.

You will configure SSH key-based authentication, organize the servers using a custom Ansible inventory, and run Ansible ad-hoc commands across individual hosts and inventory groups.

---

# Task 1 — Create the Multi-Host Lab Structure

## Goal

Create a separate project directory for the multi-host lab and prepare the Terraform, Ansible, and documentation files.

This project will use the Git repository and Ansible controller prepared in Assignment 01.

### Evidence

#### Screenshot 1 — Terminal showing the complete `ansible-adhoc-lab` project structure

![Screenshot 1](<Screenshot 2026-09-23 145021.png>)

---

#### Screenshot 2 — Terminal showing `git status --short` with the new project files and updated `.gitignore`

![Screenshot 2](<Screenshot 2026-09-23 223611.png>).

---

### Notes

I created the `ansible-adhoc-lab` project inside my existing `ansible-onboarding` repository. I separated the project into `terraform` and `ansible` directories so that Terraform can handle infrastructure provisioning while Ansible manages the Linux VMs. I also added Terraform state, plan, working-directory, and crash-log files to `.gitignore` to prevent them from being committed to Git.


---

# Task 2 — Create the Terraform Configuration

## Goal

Create the Terraform configuration required to provision three or four Ubuntu Linux VMs on your selected cloud platform.

Complete only one option:

- Option A — Microsoft Azure
- Option B — Amazon Web Services

Do not configure both providers for this assignment.

### Evidence

#### Screenshot 3 — Terraform configuration showing the three or four server roles and the `for_each` or `count` implementation

![Screenshot 3](<Screenshot 2026-09-24 102812.png>)

---

#### Screenshot 4 — Terraform configuration showing SSH restricted to the controller IP and HTTP allowed only for web hosts

![Screenshot 4](<Screenshot 2026-09-24 102837.png>)

---

#### Screenshot 5 — Terraform output configuration showing how public IP addresses are associated with the server roles

![Screenshot 5](<Screenshot 2026-09-24 102928.png>)

---

### Notes

I used Terraform to prepare, validate, plan, and deploy the infrastructure for my Ansible ad-hoc lab. I ran `terraform fmt` to format the Terraform configuration, followed by `terraform fmt -check` to confirm that the files were correctly formatted. I then ran `terraform init` to initialize the AWS provider and `terraform validate` to check that the configuration was valid.

After reviewing the Terraform plan, I confirmed that Terraform would create 10 resources, including the VPC, subnet, internet gateway, route table, security group, SSH key pair, and three EC2 instances for `web1`, `app1`, and `db1`. I then ran `terraform apply` to provision the infrastructure on AWS.

The deployment completed successfully with 10 resources added. Terraform also provided the public IP addresses of the three servers, which I will use to create the Ansible inventory and connect to the VMs.


---

# Task 3 — Provision the Infrastructure with Terraform

## Goal

Initialize and validate the Terraform configuration, review the execution plan, provision the selected three or four VMs, and retrieve their public IP addresses.

### Evidence

#### Screenshot 6 — Final `terraform apply` output showing `Apply complete`

![Screenshot 6](<Screenshot 2026-09-24 103530.png>)

---

#### Screenshot 7 — `terraform output public_ips` showing the role-to-IP mapping for all three or four VMs

![Screenshot 7](<Screenshot 2026-09-24 103530-1.png>)

---

#### Screenshot 8 — Azure Portal or AWS Management Console showing all three or four VMs in the `Running` state, with their role-based names visible

![Screenshot 8](<Screenshot 2026-09-24 103743.png>)

---

### Notes

I tested SSH connectivity from my WSL2 Ansible controller to the AWS EC2 instances. I successfully connected to `web1` using its public IP and verified its Linux hostname. The hostname initially showed the default AWS hostname, so I changed it to `web1` and verified the change successfully. This confirmed that SSH key authentication and network connectivity from the controller to the VM are working.

---

# Task 4 — Verify SSH Key-Based Access

## Goal

Verify that each managed VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 9 — Terminal showing successful SSH hostname output from all VMs

![Screenshot 9](<Screenshot 2026-09-24 110909.png>)

---

### Notes

I tested SSH connectivity from my WSL2 controller to all three AWS EC2 instances using their public IP addresses and the SSH key configured in Assignment 01. I verified that each server was reachable and checked its Linux hostname using the `hostname` command. The default hostnames were changed to match their assigned roles: `web1`, `app1`, and `db1`. This confirmed that SSH authentication and network connectivity between the Ansible controller and all three managed servers are working correctly.


---

# Task 5 — Create the Custom Ansible Inventory

## Goal

Create an Ansible inventory file that groups the managed VMs by role.

The inventory allows Ansible to run commands against all servers, or only specific groups such as `web`, `app`, or `db`.

### Evidence

#### Screenshot 10 — `inventory.ini` showing the `web`, `app`, and `db` groups

![Screenshot 10](<Screenshot 2026-09-24 112705.png>)

---

#### Screenshot 11 — Output of `ansible-inventory -i inventory.ini --graph`

![Screenshot 11](<Screenshot 2026-09-24 113023.png>)

---

### Notes

I created a custom Ansible inventory with three server groups: `web`, `app`, and `db`. Each server was assigned its AWS public IP, with `ubuntu` configured as the SSH user and my Ed25519 key set for authentication.

I also configured the inventory to use the `ubuntu` SSH user and the Ed25519 private key created during Assignment 01. I created a separate `ansible.cfg` inside the Assignment 02 `ansible` directory with host key checking disabled.


---

# Task 6 — Run Ansible Ad-Hoc Commands

## Goal

Run Ansible ad-hoc commands from the controller to verify connectivity, check server information, and manage packages and services across inventory groups.

This task proves that the inventory is working and that Ansible can control multiple managed VMs without writing a playbook.

### Evidence

#### Screenshot 12 — Output of `ansible all -i inventory.ini -m ping`

![Screenshot 12](<Screenshot 2026-09-24 113224.png>)

---

#### Screenshot 13 — Output of `ansible all -i inventory.ini -m command -a "uptime"`

![Screesnhot 13](<Screenshot 2026-09-24 113801.png>)

---

#### Screenshot 14 — Output of `ansible web -i inventory.ini -m apt -a "name=nginx state=present update_cache=yes" --become`

![Screenshot 14](<Screenshot 2026-09-24 114140.png>)

---

#### Screenshot 15 — Output of `ansible web -i inventory.ini -m service -a "name=nginx state=started enabled=yes" --become`

![Screenshot 15](<Screenshot 2026-09-24 114318.png>)

---

#### Screenshot 16 — Output of `ansible all -i inventory.ini -m apt -a "name=htop state=present update_cache=yes" --become`

![Screenshot 16](<Screenshot 2026-09-24 114619.png>)

---

#### Screenshot 17 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx"`

![Screenshot 17](<Screenshot 2026-09-24 115300.png>)

---

### Notes

I ran `ansible all -i inventory.ini -m ping` and confirmed that Ansible successfully connected to all three servers, returning `pong` for `web1`, `app1`, and `db1`. This confirmed that the inventory and SSH configuration are working correctly.

I used Ansible ad-hoc commands to manage and inspect the three Ubuntu servers. I verified connectivity, checked the remote user, server uptime, disk space, and memory. I installed and started Nginx on the web server, installed htop on all three servers, and confirmed that Nginx was active on web1. This demonstrated how Ansible can perform quick administrative and monitoring tasks across specific server groups without using a playbook.


---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

[LinkedIn Post](https://www.linkedin.com/posts/nwoke-onyinye_aws-terraform-ansible-ugcPost-7508845232579354625-H2m1/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c)

---

#### Screenshot — Published LinkedIn post

![LinkedIn Image](<Screenshot 2026-09-24 121121.png>)

---

# Assignment Questions

Answer the following in your own words:

**1. What is the purpose of an Ansible inventory file?**

An Ansible inventory file tells Ansible which servers it needs to manage and how to connect to them. In my project, the inventory contained the IP addresses of my three AWS servers and grouped them into web, app, and db. It also specified the SSH user and private key Ansible should use.

---

**2. What is the difference between the `web`, `app`, and `db` groups in your inventory?**

web contains web1, which I used for web-server tasks such as installing and running Nginx.
app contains app1, which represents the application server.
db contains db1, which represents the database server.

Grouping the servers makes it possible to target a specific type of server without running the command on every server.

---

**3. What does the Ansible `ping` module verify?**

The Ansible ping module verifies that Ansible can successfully connect to a managed server and communicate with it. It is not checking whether the server responds to a normal network ping. In my project, receiving pong from all three servers confirmed that my SSH connection and Ansible setup were working.

---

**4. Why do package installation commands require `--become`?**

Package installation normally requires administrator/root privileges because it changes software installed on the operating system. The ubuntu user I used does not normally have those privileges directly, so --become allows Ansible to temporarily perform the task with elevated privileges.

For example, I used --become when installing Nginx and htop.

---

**5. When would you use an ad-hoc command instead of a playbook?**

I would use an ad-hoc command when I need to perform a quick, one-time task or check something on a server. For example, I used ad-hoc commands to check uptime, disk space and memory, install htop, and verify that Nginx was running.

For larger or repeated tasks that need to be consistent and documented, I would use a playbook instead.

---

**6. What is one challenge you faced while setting up SSH or inventory, and how did you fix it?**

One challenge was making sure Ansible used the correct SSH key and connection details for the three AWS servers. I created a custom inventory with each server's public IP, set the SSH user to ubuntu, and specified my Ed25519 private key at ~/.ssh/id_ed25519. I then tested the connections with Ansible's ping module and received pong from all three servers, confirming that the SSH and inventory configuration was working correctly.

---

# Required Files

Confirm that the following files are included in your assignment workspace:

- [ ] `ansible-adhoc-lab/README.md`
- [ ] `ansible-adhoc-lab/terraform/providers.tf`
- [ ] `ansible-adhoc-lab/terraform/main.tf`
- [ ] `ansible-adhoc-lab/terraform/variables.tf`
- [ ] `ansible-adhoc-lab/terraform/outputs.tf`
- [ ] `ansible-adhoc-lab/ansible/inventory.ini`
- [ ] Updated `.gitignore`

---

# Submission Instructions

- Add all required screenshots from the tasks.
- Full Name must be visible in required screenshots.
- Mention whether you used Azure or AWS.
- Mention whether you used the three-VM option or four-VM option.
- Add the public IP addresses of the VMs, redacted if preferred.
- Add your `inventory.ini` proof.
- Add a short explanation of what you learned.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, Terraform state files, cloud credentials, passwords, access keys, secret keys, account IDs, or subscription IDs.
- Submit only one Google Doc link.

---

# Completion Checklist

- [ ] Task 1: `ansible-adhoc-lab` project structure created
- [ ] Task 1: `.gitignore` updated for Terraform files
- [ ] Task 2: Terraform configuration created
- [ ] Task 2: Server roles defined for either three or four VMs
- [ ] Task 2: `count` or `for_each` used
- [ ] Task 2: SSH restricted to the controller public IP
- [ ] Task 2: HTTP allowed only for web hosts
- [ ] Task 2: Terraform output maps roles to public IPs
- [ ] Task 3: Terraform initialized successfully
- [ ] Task 3: Terraform configuration validated
- [ ] Task 3: Terraform apply completed successfully
- [ ] Task 3: All selected VMs are running
- [ ] Task 4: SSH key-based access works for every VM
- [ ] Task 5: `inventory.ini` contains `web`, `app`, and `db` groups
- [ ] Task 5: `ansible-inventory -i inventory.ini --graph` shows the correct groups
- [ ] Task 6: `ansible all -i inventory.ini -m ping` returns `SUCCESS`
- [ ] Task 6: Ad-hoc commands run successfully
- [ ] Task 6: `--become` was used for package and service tasks
- [ ] Task 6: Nginx is active on the `web` group
- [ ] Screenshots 1–17 are included
- [ ] Assignment questions are answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information is exposed
- [ ] Google Doc is accessible

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