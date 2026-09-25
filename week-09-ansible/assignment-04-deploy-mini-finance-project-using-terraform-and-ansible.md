# Assignment 04 — Deploy Mini Finance on Azure Using Terraform and Ansible

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will provision Azure infrastructure using Terraform and deploy the Mini Finance website using an Ansible multi-play playbook.

Terraform will create the Azure Virtual Machine and networking resources. Ansible will install Nginx, clone the Mini Finance repository, deploy the website, and verify the deployment.

---

# Task 1 — Create the Project Structure

## Goal

Create separate directories and files for the Terraform infrastructure and Ansible configuration.

### Evidence

#### Screenshot 1 — Terminal or VS Code showing the complete `mini-finance` project structure

![Screenshot 1](<Screenshot 2026-09-24 173133.png>)

---

### Notes

### Task 1 — Project Setup

I created my **Mini Finance AWS project** with separate Terraform and Ansible folders. I configured Terraform to provision the AWS network, security group, Ubuntu EC2 instance, and SSH access.

I also added a `.gitignore` to keep Terraform state files and sensitive files out of Git.

Finally, I ran `terraform init` successfully.

**What I learned:** Terraform will handle the AWS infrastructure, while Ansible will handle the server configuration and website deployment.

---

# Task 2 — Create the Azure Infrastructure Using Terraform

## Goal

Use Terraform to provision an Ubuntu Virtual Machine with the required Azure networking and security resources.

### Evidence

#### Screenshot 2 — Terraform code showing the `Allow-SSH` rule for port `22` and the `Allow-HTTP` rule for port `80`

![Screenshot 2](<Screenshot 2026-09-24 173514.png>)

---

#### Screenshot 3 — Terraform code showing the association between `nsg-mini-finance` and `nic-mini-finance`

![Screenshot 3](<Screenshot 2026-09-24 173935.png>)

---

### Notes

### Task 2 — AWS Infrastructure

I used Terraform to define the AWS infrastructure for my Mini Finance application, including a VPC, public subnet, Internet Gateway, route table, Security Group, SSH key pair, and Ubuntu EC2 instance.

The Security Group allows **SSH from my IP only** and **HTTP from the internet**.

**What I learned:** Terraform can create and connect the main AWS infrastructure components consistently, instead of configuring them manually.


---

# Task 3 — Initialize and Apply the Terraform Configuration

## Goal

Format and validate the Terraform configuration, review the execution plan, and provision the Azure infrastructure.

### Evidence

#### Screenshot 4 — End of the `terraform apply` output showing `Apply complete!` with no errors

![Screenshot 4](<Screenshot 2026-09-24 185528.png>)

---

#### Screenshot 5 — Output of `terraform output public_ip` showing the VM’s public IP address

Screesnhot 5

---

### Notes

### Task 3 — Terraform Deployment

I validated, planned, and applied my Terraform configuration successfully. Terraform created **8 AWS resources**, including the VPC, subnet, Internet Gateway, route table, Security Group, key pair, and Ubuntu EC2 instance.

The EC2 instance was created successfully and Terraform returned its public IP.

**What I learned:** `terraform plan` lets me review changes before deployment, while `terraform apply` creates the infrastructure in AWS.


---

# Task 4 — Verify Passwordless SSH Access

## Goal

Confirm that the Ansible controller can connect to the Terraform-provisioned Azure VM using SSH key authentication.

### Evidence

#### Screenshot 6 — Passwordless SSH command and the returned `mini-finance` hostname

![Screesnhot 6](<Screenshot 2026-09-24 192020.png>)
---

### Notes

### Task 4 — SSH Verification

I connected successfully to my AWS Ubuntu EC2 instance using my SSH key (`~/.ssh/id_ed25519`). I verified that the server hostname is `mini-finance`, matching the project name.

**What I learned:** SSH allows me to securely connect to and manage my AWS server remotely without using a password.


---

# Task 5 — Create the Ansible Inventory and Verify Connectivity

## Goal

Add the Terraform-provisioned Azure VM to the Ansible inventory and confirm that Ansible can connect to it.

### Evidence

#### Screenshot 7 — Ansible ping output showing `SUCCESS` and `pong` from the Azure VM

![Screesnhot 7](<Screenshot 2026-09-24 205216.png>)

---

### Configuration File

Copy and paste the complete contents of your `ansible/inventory.ini` file below:


[web]
mini-finance ansible_host=13.49.74.135

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/home/onyinw/.ssh/id_ed25519
```

---

# Task 6 — Create the Multi-Play Ansible Playbook

## Goal

Create one Ansible playbook containing separate plays to install Nginx, deploy the Mini Finance website, and verify the deployment.

### Evidence

#### Screenshot 8 — `site.yml` showing Play 1 and the beginning of Play 2

Screenshot must show:

- Play 1 targeting the `web` group
- Installation of `nginx`, `git`, and `rsync`
- Nginx service configured as started and enabled
- Beginning of Play 2 with the Git repository URL and synchronization task
![Screenshot 8](<Screenshot 2026-09-24 222014.png>)
![Screenshot 8.1](<Screenshot 2026-09-24 222029.png>)

---

#### Screenshot 9 — `site.yml` showing the deployment destination, handler, and Play 3 verification

Screenshot must show:

- Website destination `/var/www/html/`
- Ownership set to `www-data:www-data`
- Nginx reload handler
- Play 3 targeting `localhost`
- The `uri` verification and `assert` condition

![Screenshot 9](<Screenshot 2026-09-24 222519.png>)
---

### Configuration File

Copy and paste the complete contents of your `ansible/site.yml` file below:

---
- name: Install required packages and configure Nginx
  hosts: web
  become: true
  tasks:
    - name: Update APT package cache
      ansible.builtin.apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Nginx, Git and rsync
      ansible.builtin.apt:
        name:
          - nginx
          - git
          - rsync
        state: present

    - name: Start and enable Nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

- name: Deploy Mini Finance website
  hosts: web
  become: true
  tasks:
    - name: Clone or update Mini Finance repository
      ansible.builtin.git:
        repo: https://github.com/pravinmishraaws/mini_finance
        dest: /opt/mini-finance
        version: HEAD
        force: true
        update: true

    - name: Synchronize website files to Nginx web root
      ansible.posix.synchronize:
        src: /opt/mini-finance/
        dest: /var/www/html/
        delete: true
        checksum: true
        owner: false
        group: false
        rsync_opts:
          - "--exclude=.git"
      delegate_to: "{{ inventory_hostname }}"
      notify: Reload nginx

    - name: Set web root ownership
      ansible.builtin.file:
        path: /var/www/html/
        owner: www-data
        group: www-data
        recurse: true

  handlers:
    - name: Reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded

- name: Verify Mini Finance website
  hosts: localhost
  connection: local
  gather_facts: false
  become: false
  tasks:
    - name: Send HTTP GET request
      ansible.builtin.uri:
        url: "http://{{ hostvars[item].ansible_host }}"
        status_code: 200
      loop: "{{ groups['web'] }}"
      register: website_checks

    - name: Confirm HTTP 200
      ansible.builtin.assert:
        that:
          - item.status == 200
        success_msg: "{{ item.item }} returned HTTP {{ item.status }}"
        fail_msg: "{{ item.item }} did not return HTTP 200"
      loop: "{{ website_checks.results }}"
```

---

# Task 7 — Validate and Run the Ansible Playbook

## Goal

Validate the syntax of the multi-play Ansible playbook and run it to install Nginx, deploy the Mini Finance website, and verify the deployment.

### Evidence

#### Screenshot 10 — Successful playbook syntax check showing `playbook: site.yml`

![Screesnhot 10](<Screenshot 2026-09-24 205938.png>)

---

#### Screenshot 11 — Play 3 output showing the successful HTTP verification and assertion

![Screenshot 11](<Screenshot 2026-09-24 230721.png>)

---

#### Screenshot 12 — Final `PLAY RECAP` showing `failed=0` and `unreachable=0`

![Screenshot 12](<Screenshot 2026-09-24 230351.png>)

---

### Notes

I verified the Mini Finance website after deploying it with Ansible. The website was successfully deployed to the Nginx web root at `/var/www/html/`, and the playbook included a handler to reload Nginx whenever the website files changed. The final play verified that the website returned HTTP 200 successfully.

**What I learned:** Ansible can automate both application deployment and verification, helping ensure that the website is correctly configured and accessible after deployment.


---

# Task 8 — Test the Mini Finance Website in a Browser

## Goal

Confirm that the Mini Finance website is publicly accessible through the Azure VM’s public IP address.

### Evidence

#### Screenshot 13 — Mini Finance website successfully loading in the browser, with the Azure VM’s public IP address visible in the address bar

![Screenshot 13](<Screenshot 2026-09-24 230215.png>)

---

### Website URL

Add your deployed website URL below:

```text
http://13.49.0.9/
```

---

# Task 9 — Create the Project README

## Goal

Create a `README.md` file to document the Mini Finance infrastructure and deployment project.

### Evidence

#### Screenshot 14 — Completed `README.md` displayed in the VS Code Markdown preview or terminal

![Screenshot 14](<Screenshot 2026-09-24 231140.png>)

---

### README Content

Copy and paste the complete contents of your `README.md` file below:

```markdown
# Mini Finance — AWS Terraform and Ansible

## Project Overview

This project demonstrates how Terraform and Ansible can work together to deploy a static Mini Finance website on AWS.

Terraform is responsible for provisioning the AWS infrastructure, while Ansible handles server configuration, Nginx installation, website deployment, and verification.

## Architecture

The deployment consists of:

* AWS VPC
* Public subnet
* Internet Gateway
* Route table
* Security group
* Ubuntu EC2 instance
* Nginx web server
* Mini Finance static website

### Traffic Flow

```text
Internet
   |
   v
AWS Internet Gateway
   |
   v
Public Subnet
   |
   v
Ubuntu EC2
   |
   v
Nginx
   |
   v
Mini Finance Website
```

## Terraform

Terraform provisions the AWS infrastructure required for the application.

### Terraform responsibilities

* Create the VPC
* Create the public subnet
* Create the Internet Gateway
* Create the public route table
* Create the security group
* Create the EC2 key pair
* Launch the Ubuntu EC2 instance
* Assign a public IP address
* Configure the server hostname as `mini-finance`

### Terraform workflow

```bash
terraform init
terraform validate
terraform plan
terraform apply
```

Terraform successfully created the required infrastructure.

## Ansible

Ansible is used after Terraform has provisioned the EC2 instance.

### Ansible responsibilities

* Connect to the EC2 instance using SSH
* Update the APT package cache
* Install Nginx
* Install Git
* Install rsync
* Start and enable Nginx
* Clone the Mini Finance repository
* Synchronize the website files to `/var/www/html/`
* Set the correct web-root ownership
* Reload Nginx when website files change
* Verify that the website returns HTTP 200

## Mini Finance Repository

The website source was obtained from:

`https://github.com/pravinmishraaws/mini_finance`

## Ansible Verification

Ansible successfully verified that the deployed website returned HTTP 200.

The playbook was also run a second time to test idempotency.

The second run completed with:

```text
localhost     : changed=0
mini-finance  : changed=0
```

This confirms that the server remained in the desired state without unnecessary changes.

## Project Structure

```text
mini-finance/
├── .gitignore
├── README.md
├── terraform/
│   ├── providers.tf
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── terraform.tfvars
└── ansible/
    ├── inventory.ini
    └── site.yml
```

## Key Learning

This project demonstrated the separation of responsibilities between Terraform and Ansible.

**Terraform provisions the infrastructure.**

**Ansible configures the server and deploys the application.**

Using both tools together makes infrastructure and application deployment more repeatable, consistent, and easier to manage.

## Security

SSH access was restricted to the configured public IP address, while HTTP access was allowed for the public website.

Sensitive files such as Terraform state files, `.pem` files, private keys, and `terraform.tfvars` are excluded through `.gitignore`.

## Cleanup

After completing the assignment, the AWS resources can be removed with:

```bash
terraform destroy
```

```

---

# LinkedIn Post Required

## Evidence

#### Screenshot 15 — Published LinkedIn post showing the text and at least one deployment screenshot

Add your screenshot here.

---

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`https://www.linkedin.com/posts/nwoke-onyinye_aws-terraform-ansible-ugcPost-7509014301177901056-Xboa/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c`

---

### LinkedIn Submission Notes

**One challenge you faced and how you fixed it:**

The GitHub repository URL provided in the assignment was incorrect. When Ansible tried to clone it, the task remained stuck because Git could not access the repository. I tested the repository directly from the EC2 instance, identified the correct repository URL, updated the Ansible playbook and reran the deployment successfully.

---

**One real-world example where you can use this learning:**

This approach can be used to deploy websites or applications consistently across multiple AWS servers. Terraform can provision the infrastructure, while Ansible can automatically install the required software, configure the servers and deploy the application. This reduces manual work and makes deployments faster and repeatable.

---

# Assignment Questions

Answer the following in your own words:

**1. What did you provision using Terraform in this assignment?**

I used Terraform to provision the AWS infrastructure for the Mini Finance application. This included a VPC, public subnet, Internet Gateway, route table, security group, EC2 key pair and Ubuntu EC2 instance with a public IP address.

---

**2. What did Ansible configure and deploy in this assignment?**

Ansible configured the EC2 server by installing Nginx, Git and rsync. It started and enabled Nginx, cloned the Mini Finance project from GitHub, synchronized the website files to /var/www/html/, set the web-root ownership and verified that the website returned HTTP 200.

---

**3. Why is SSH access on port `22` restricted to your public IP address?**

SSH is used to remotely manage the server. Restricting port 22 to my public IP reduces exposure to unauthorized SSH connection attempts from the wider internet.

---

**4. Why is HTTP port `80` open to the internet?**

Port 80 is open because the Mini Finance website is a public website that needs to be accessible through a web browser over HTTP.
---

**5. What is the purpose of the Ansible inventory file?**

The inventory tells Ansible which servers to manage and provides the information required to connect to them, such as the server address, username and SSH key.

---

**6. Why does the playbook use separate plays for install, deploy, and verify?**

The separate plays keep the automation organized by responsibility. The first play prepares the server, the second deploys the website and the third verifies that the deployment works. This makes the playbook easier to understand, maintain and troubleshoot.
---

**7. Why is `rsync` useful when deploying website files?**

Rsync efficiently synchronizes files between locations. It can identify what needs to be updated instead of unnecessarily copying everything, making repeated website deployments more efficient.

---

**8. What does the Ansible `uri` module verify in this assignment?**

The uri module sends an HTTP request to the website and checks that the server responds with HTTP status code 200. This confirms that the deployed website is accessible.

9. What issue did you face during this assignment, and how did you fix it.

I initially used the repository URL provided in the assignment, but it was not the correct GitHub repository URL. The Ansible Git task remained stuck while trying to clone it. I tested the repository directly, found the correct mini_finance repository URL, updated site.yml and reran the playbook successfully.
---

**10. What did you learn from using Terraform and Ansible together?**

I learned how Terraform and Ansible can work together while handling different parts of a deployment. Terraform provisions the infrastructure, while Ansible configures the server and deploys the application. This separation makes the process more repeatable and reduces manual configuration.

---

# Required Files

Confirm that the following files are included in your assignment folder:

- [ ] `.gitignore`
- [ ] `README.md`
- [ ] `terraform/providers.tf`
- [ ] `terraform/main.tf`
- [ ] `terraform/variables.tf`
- [ ] `terraform/outputs.tf`
- [ ] `ansible/inventory.ini`
- [ ] `ansible/site.yml`

---

# Submission Instructions

- Add all required screenshots in the correct order.
- Full Name must be visible in required screenshots.
- Add the Azure VM public IP address.
- Add the final Mini Finance website URL.
- Paste `inventory.ini`, `site.yml`, and `README.md` as editable text.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, Azure credentials, subscription IDs, Terraform state contents, or other sensitive information.
- Submit only one Google Doc link.
- Ensure that anyone with the link can view the document.
- Test the Google Doc link in an incognito or private browser window before submitting.

---

# Completion Checklist

- [ ] Task 1: `mini-finance` project structure created
- [ ] Task 1: `.gitignore` created
- [ ] Task 2: Terraform Azure infrastructure code created
- [ ] Task 2: `Allow-SSH` rule configured for port `22`
- [ ] Task 2: `Allow-HTTP` rule configured for port `80`
- [ ] Task 2: NSG associated with the Network Interface
- [ ] Task 3: `terraform fmt` completed
- [ ] Task 3: `terraform init` completed
- [ ] Task 3: `terraform validate` completed successfully
- [ ] Task 3: `terraform apply` completed successfully
- [ ] Task 3: `terraform output public_ip` displayed the VM public IP
- [ ] Task 4: Passwordless SSH works from the Ansible controller
- [ ] Task 5: `inventory.ini` created
- [ ] Task 5: Ansible ping returns `SUCCESS` and `pong`
- [ ] Task 6: `site.yml` contains three separate plays
- [ ] Task 6: Play 1 installs Nginx, Git, and rsync
- [ ] Task 6: Play 2 clones and deploys the Mini Finance website
- [ ] Task 6: Play 3 verifies HTTP status code `200`
- [ ] Task 7: Playbook syntax check passes
- [ ] Task 7: Ansible playbook completes successfully
- [ ] Task 7: Final recap shows `failed=0` and `unreachable=0`
- [ ] Task 8: Mini Finance website loads in the browser
- [ ] Task 8: Azure VM public IP is visible in the browser screenshot
- [ ] Task 9: `README.md` completed
- [ ] Screenshots 1–15 are included
- [ ] `inventory.ini`, `site.yml`, and `README.md` are pasted as editable text
- [ ] Assignment questions are answered
- [ ] LinkedIn post published with Anyone visibility
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