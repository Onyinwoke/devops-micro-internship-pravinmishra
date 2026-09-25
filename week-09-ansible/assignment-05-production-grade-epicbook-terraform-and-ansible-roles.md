# Assignment — Deploy EpicBook with Terraform and Ansible Roles

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook web application using Terraform and Ansible roles.

Terraform provisions the cloud infrastructure, including one Ubuntu VM and one managed MySQL database. Ansible roles configure the VM, install required software, deploy the EpicBook application, configure Nginx, connect the app to the managed MySQL database, and verify the deployment.

---

# Task 1 — Set Up the Project Folder Layout

## Goal

Create the project folder structure for Terraform and Ansible roles.

Terraform will be used to provision the cloud infrastructure. Ansible roles will be used to configure the VM and deploy the EpicBook application.

### Evidence

#### Screenshot 1 — Terminal showing the completed `epicbook-prod` project structure

![Screenshot 1](<Screenshot 2026-09-25 005239.png>)

---

### Notes

Answer the following in your own words:

**1. Which cloud provider did you choose for this assignment?**

  I chose to use AWS.

---

**2. Why is it useful to keep Terraform files and Ansible files in separate folders?**

It shows better organization. Terraform builds the infrastructure.
Ansible configures the infrastructure and deploys the application.

---

**3. What is the purpose of the `roles` directory in Ansible?**

The roles directory organizes Ansible automation into reusable components. Each role groups related tasks, templates, and configuration for a specific purpose, such as installing common packages, configuring Nginx, or deploying the EpicBook application. This makes playbooks cleaner, easier to maintain, and reusable across different projects.

---

# Task 2 — Provision the Infrastructure with Terraform

## Goal

Run Terraform to provision the cloud infrastructure for the EpicBook deployment.

Terraform will create the VM, managed MySQL database, networking, security rules, and required outputs.

### Evidence

#### Screenshot 2 — `terraform apply` completed successfully

![Screenshot 2](<Screenshot 2026-09-25 012327.png>)

---

#### Screenshot 3 — Output of `terraform output`

![Screenshot 3](<Screenshot 2026-09-25 012429.png>)

---

#### Screenshot 4 — Azure Portal or AWS Console showing the VM running

![Screenshot 4](<Screenshot 2026-09-25 013059.png>)

---

#### Screenshot 5 — Azure Portal or AWS Console showing the managed MySQL database created

![Screenshot](<Screenshot 2026-09-25 013232.png>)

---

### Notes

Answer the following in your own words:

**1. What resources did Terraform create for this assignment?**

Terraform created 13 AWS resources, including a VPC, public and private subnets, an Internet Gateway, a route table, security groups, an EC2 Ubuntu server, an SSH key pair, an RDS subnet group, and an RDS MySQL database.

---

**2. Why should you review `terraform plan` before running `terraform apply`?**

terraform plan shows what Terraform intends to create, change, or destroy before making any actual changes. Reviewing it helps identify configuration errors, unexpected resources, or incorrect settings before they affect the AWS environment

---

**3. Why should database passwords not be shown in Terraform output?**

Database passwords are sensitive credentials. Showing them in Terraform output can expose them to other people or accidentally store them in logs, screenshots, or shared files. Keeping passwords hidden helps protect the database and prevent unauthorized access.

---

# Task 3 — Verify SSH Key-Based Access

## Goal

Verify that the cloud VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 6 — Successful SSH hostname check from the Ansible controller

![Screenshot 6](<Screenshot 2026-09-25 012716-1.png>)

---

### Notes

Answer the following in your own words:

**1. What command did you use to verify SSH access?**

I used the SSH command with my private key to connect to the EC2 server and run the hostname command:

ssh -i ~/.ssh/id_ed25519 ubuntu@13.61.0.25 "hostname"

---

**2. What proves that SSH key-based access worked successfully?**

The server returned its hostname, ip-10-20-1-46, without asking for a password. This proves that my SSH key was accepted and I successfully connected to the EC2 server.

---

**3. What would you check if SSH returned `Permission denied (publickey)`?**

I would check that I am using the correct private key, the correct username (ubuntu), and the correct EC2 public IP address. I would also confirm that the matching public key was installed on the EC2 instance and that the security group allows SSH access from my current IP address.

---

# Task 4 — Create the Ansible Inventory and Configuration

## Goal

Create the Ansible inventory file and local Ansible configuration for the EpicBook VM.

The inventory tells Ansible which VM to manage and which SSH user to use.

### Evidence

#### Screenshot 7 — `inventory.ini` showing the VM under the `web` group

![Screenshot 7](<Screenshot 2026-09-25 014319.png>).

---

#### Screenshot 8 — Output of `ansible-inventory -i inventory.ini --graph`

![Screenshot 8](<Screenshot 2026-09-25 014340.png>)

---

#### Screenshot 9 — Output of `ansible web -i inventory.ini -m ping`

![Screenshot 9](<Screenshot 2026-09-25 014419.png>)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `inventory.ini`?**

inventory.ini tells Ansible which servers it should manage. It contains the server names, IP addresses, usernames, and connection details Ansible needs to access the machines.

---

**2. What does `ansible_host` store?**

ansible_host stores the IP address or hostname that Ansible uses to connect to the server.

---

**3. What does `ansible_ssh_private_key_file` tell Ansible?**

It tells Ansible which private SSH key to use when connecting to the server. This allows Ansible to authenticate without using a password.

---

**4. Why is `host_key_checking = False` used only for this temporary lab?**

It prevents Ansible from stopping to ask for confirmation when connecting to a new server. It is convenient for a temporary lab, but disabling host key checking reduces SSH security, so it should not normally be used in a production environment.

---

# Task 5 — Create the Main Ansible Playbook

## Goal

Create the main Ansible playbook that runs the required roles in the correct order.

The `site.yml` file will call the `common`, `nginx`, and `epicbook` roles.

### Evidence

#### Screenshot 10 — `site.yml` showing the roles in the correct order

![Screenshot 10](<Screenshot 2026-09-25 015459.png>)

---

#### Screenshot 11 — Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`

![Screenshot 11](<Screenshot 2026-09-25 015621.png>)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `site.yml`?**

site.yml is the main Ansible playbook for the project. It tells Ansible which servers to manage and which roles to run to configure and deploy the EpicBook application.

---

**2. Why should the roles run in the order `common`, `nginx`, and `epicbook`?**

The common role installs the basic packages needed by the server. The nginx role then configures the web server. Finally, the epicbook role installs and deploys the application. This order ensures that the required dependencies are available before each stage needs them.

---

**3. What does `become: true` allow Ansible to do?**

become: true allows Ansible to use elevated privileges, such as sudo, on the server. This is necessary for tasks such as installing packages and modifying system files and services.

---

# Task 6 — Create the `common` Role

## Goal

Create the `common` role to prepare the Ubuntu VM with the basic packages required for the EpicBook deployment.

This role handles the common server setup before Nginx and the application are configured.

### Evidence

#### Screenshot 12 — `roles/common/tasks/main.yml` showing the common setup tasks

![Screenshot 12](<Screenshot 2026-09-25 020018.png>)

---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `common` role?**

The common role prepares the server by updating the package list and installing basic tools that the application deployment needs, such as Git, Curl, Unzip, software-properties-common, and the MySQL client.

---

**2. Why should Nginx installation not be placed inside the `common` role?**

Nginx is specifically responsible for serving web traffic and acting as a reverse proxy for the application. Keeping it in its own role makes the automation organized, reusable, and easier to maintain.

---

**3. Why is `mysql-client` useful in this deployment?**

The MySQL client allows us to connect to the managed RDS MySQL database from the EC2 server. It can also be used to test the database connection and import the application's SQL files.

---

# Task 7 — Create the `nginx` Role

## Goal

Create the `nginx` role to install Nginx and configure it as a reverse proxy for the EpicBook application.

Nginx will receive browser traffic on port `80` and forward it to the EpicBook Node.js application running on the VM.

### Evidence

#### Screenshot 13 — `roles/nginx/tasks/main.yml` showing Nginx installation and site configuration tasks

![Screenshot 13](<Screenshot 2026-09-25 020723.png>)

---

#### Screenshot 14 — `roles/nginx/templates/epicbook.conf.j2` showing the reverse proxy configuration

![Screenshot 14](<Screenshot 2026-09-25 020902.png>)

---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `nginx` role?**

The nginx role installs and configures Nginx on the EC2 server. It sets up the EpicBook reverse proxy, removes the default site, enables the EpicBook configuration, and makes sure Nginx is running and enabled.

---

**2. Why is Nginx configured as a reverse proxy in this deployment?**

Nginx receives requests from users on port 80 and forwards them to the EpicBook application running on port 8080. This allows Nginx to handle public web traffic while the application runs separately on its own port.

---

**3. Why should the application port come from `group_vars/web.yml` instead of being hard-coded?**

Keeping the application port in group_vars/web.yml makes the configuration easier to change and reuse. If the application port changes, we only need to update the variable instead of changing the Nginx configuration itself.

---

# Task 8 — Create the `epicbook` Role

## Goal

Create the `epicbook` role to deploy the EpicBook application, connect it to the managed MySQL database, and run the application on port `8080` using PM2.

### Evidence

#### Screenshot 15 — `roles/epicbook/tasks/main.yml` showing application deployment tasks

![Screenshot 15](<Screenshot 2026-09-25 021600.png>)

---

#### Screenshot 16 — Task or file showing how the database connection is configured, with secrets hidden

![Screenshot 16](<Screenshot 2026-09-25 021732.png>)

---

#### Screenshot 17 — Task or output showing the EpicBook application managed by PM2

![Screenshot 17](<Screenshot 2026-09-25 025126.png>)
---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `epicbook` role?**

The epicbook role is responsible for deploying and running the EpicBook Node.js application. It installs Node.js and PM2, clones the EpicBook application from GitHub, installs the application dependencies, configures the database connection, imports the required SQL files, and starts the application with PM2 on port 8080.

---

**2. Why is PM2 used for the EpicBook Node.js application?**

PM2 is used to manage the Node.js application as a background process. It keeps the EpicBook application running, allows it to be restarted when necessary, and makes it easier to monitor the application. This is more suitable for a server environment than simply running the Node.js server manually from a terminal.

---

**3. Why should database passwords not be hard-coded in public files?**

Database passwords should not be hard-coded because anyone who gains access to the file or repository could obtain the database credentials. This could allow unauthorized access to the database. Keeping passwords outside the code and supplying them through environment variables or a secret-management system reduces this risk.

---

**4. What does it mean for the application to run on port `8080` while Nginx listens on port `80`?**

It means the Node.js application listens internally on port 8080, while Nginx receives public HTTP requests on port 80. Nginx then acts as a reverse proxy and forwards those requests to the application on port 8080.

This allows users to access the application through the normal HTTP port without needing to enter port 8080 in the browser.

---

# Task 9 — Create Group Variables

## Goal

Create reusable variables for the EpicBook deployment.

The `group_vars/web.yml` file stores values that can be reused across the Ansible roles.

### Evidence

#### Screenshot 18 — `group_vars/web.yml` showing the application, PM2, and database variables, with passwords hidden or masked

![Screenshot 18](<Screenshot 2026-09-25 024904.png>)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `group_vars/web.yml`?**

group_vars/web.yml stores variables that are shared by the hosts in the web group. It keeps configuration values in one place so that the different Ansible roles can reuse them instead of having the same values written directly inside several task files.

---

**2. Which values did you store in `group_vars/web.yml`?**

I stored the EpicBook repository URL, application deployment directory, application user, application port, PM2 process name, Nginx server name, RDS database host, database name, database username, and the database password lookup.

---

**3. How did you handle the database password securely?**

I did not write the actual database password into group_vars/web.yml or commit it to GitHub. Instead, Ansible retrieves it from the TF_VAR_db_password environment variable using an environment lookup.

This keeps the actual password separate from the project files.

---

# Task 10 — Run the Ansible Playbook

## Goal

Run the Ansible playbook to configure the VM and deploy the EpicBook application.

The playbook should run the roles in this order:

1. `common`
2. `nginx`
3. `epicbook`

### Evidence

#### Screenshot 19 — Ansible playbook output showing the roles running

![Screenshot 19](<Screenshot 2026-09-25 025655.png>)
![Screenshot 19.1](<Screenshot 2026-09-25 024754.png>)
---

#### Screenshot 20 — Final Ansible recap showing `failed=0`

![Screenshot 20](<Screenshot 2026-09-25 025706.png>) 

---

#### Screenshot 21 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx" --become`

![Screenshot 21](<Screenshot 2026-09-25 030018.png>)

---

#### Screenshot 22 — Output of `ansible web -i inventory.ini -m command -a "pm2 status"`

![Screenshot 22](<Screenshot 2026-09-25 030058.png>)

---

#### Screenshot 23 — Output of `ansible web -i inventory.ini -m command -a "curl -I http://localhost:8080"`

![Screenshot 23](<Screenshot 2026-09-25 030134.png>)

---

### Notes

Answer the following in your own words:

**1. What command did you run to execute the Ansible playbook?**

I ran ansible-playbook -i inventory.ini site.yml.

---

**2. How do you know all roles completed successfully?**

The final Ansible recap showed, epicbook : ok=28 changed=8 unreachable=0 failed=0 skipped=3 rescued=0 ignored=0

The important value is failed=0, which shows that Ansible completed without any failed tasks. unreachable=0 also confirms that Ansible was able to connect to the server.

---

**3. What proves that Nginx is active?**

The command ansible web -i inventory.ini -m command -a "systemctl is-active nginx" --become

returned:active

---

**4. What proves that PM2 is managing the EpicBook application?**

The PM2 status output showed the process named epicbook with the status:online

It also showed that the process was running under the ubuntu user. This proves that PM2 was managing the EpicBook Node.js application.

---

**5. What proves that the EpicBook application responds on port `8080`?**

The command ansible web -i inventory.ini -m command -a "curl -I http://localhost:8080"

returned a successful HTTP response with HTTP/1.1 200 OK.

This proves that the EpicBook application was responding directly on port 8080.
---

# Task 11 — Verify the EpicBook Deployment

## Goal

Verify that the EpicBook application is running, accessible in the browser, and connected to the managed MySQL database.

### Evidence

#### Screenshot 24 — Output of `curl -I http://<public_ip>`

![Screenshot 24](<Screenshot 2026-09-25 030217.png>)

---

#### Screenshot 25 — Output of the cart API test command

![Screenshot 25](<Screenshot 2026-09-25 030305.png>)

---

#### Screenshot 26 — Output of the `/cart` HTTP status check

![Screenshot 26](<Screenshot 2026-09-25 030352.png>)

---

#### Screenshot 27 — Browser showing the EpicBook application loaded from `http://<public_ip>`

![Screenshot 27](<Screenshot 2026-09-25 030440.png>)

---

### Notes

Answer the following in your own words:

**1. What HTTP response did you receive from the public application URL?**

The public application was tested using: curl -I http://13.61.0.25

The expected successful response was HTTP 200 OK, confirming that the application was accessible through the public IP and Nginx.

---

**2. What did the cart API test prove?**

The cart API test successfully returned JSON containing the cart quantity, cart ID, price, and book information for the selected book.

This proved that the public request reached the EpicBook application and that the application was able to communicate with the MySQL database successfully.

---

**3. What did the `/cart` status check return?**

The /cart status check was used to verify that the cart page was accessible through the public application URL.

A successful result is HTTP status 200, confirming that the /cart route was available.

---

**4. What issue did you face during verification, and how did you fix it?**

There was no blocking issue during the final public verification. The cart API successfully returned the expected book and cart information, showing that the application and database connection were working correctly.

During deployment, I did encounter an Ansible syntax issue because handlers had initially been placed inside the task files. I fixed this by moving the handlers into the correct handlers/main.yml files for the Nginx and EpicBook roles. After that, the playbook completed successfully with failed=0.

---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`https://www.linkedin.com/posts/nwoke-onyinye_aws-terraform-ansible-ugcPost-7509211214678212608-wug2/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c`

---

#### Screenshot — Published LinkedIn post

![LinkedIn Post](<Screenshot 2026-09-25 122455.png>)

---

# Assignment Questions

Answer the following in your own words:

**1. Why is Terraform used for infrastructure provisioning?**

Terraform is used to create and manage infrastructure as code. Instead of manually creating AWS resources through the console, I can define the infrastructure in configuration files and use Terraform to create, change, or destroy the resources consistently.

In this project, Terraform provisioned the EC2 server, RDS MySQL database, networking, and security resources.

---

**2. Why are Ansible roles useful for production-style deployments?**

Ansible roles organize automation into separate, reusable components. Each role can focus on a specific responsibility.

In this project, common prepared the server, nginx configured the web server and reverse proxy, and epicbook deployed the application. This structure makes the automation easier to understand, maintain, and reuse.

---

**3. What is the purpose of `group_vars/web.yml`?**

group_vars/web.yml provides shared variables for the web server group. It keeps application, PM2, Nginx, and database configuration values in one place so that the Ansible roles can use them consistently.

---

**4. Why should database passwords not be committed to GitHub?**

A database password committed to GitHub could be exposed to unauthorized people and potentially used to access the database. Even if the password is later removed from the visible file, it may remain in Git history.

For this project, I kept the actual password outside the repository and supplied it through an environment variable.

---

**5. What is the purpose of Nginx in this deployment?**

Nginx acts as the public-facing web server and reverse proxy. It listens for HTTP requests on port 80 and forwards them to the EpicBook Node.js application running on port 8080.

This separates the public web entry point from the application's internal port.

---

**6. Why should the managed MySQL database not be publicly accessible?**

A database should not be directly exposed to the internet because it increases the attack surface and could allow unauthorized connection attempts.

In this deployment, the RDS security group allowed MySQL traffic only from the EC2 security group. This means the application server could communicate with the database without making the database publicly accessible.

---

**7. Why is PM2 used for the EpicBook Node.js application?**

PM2 provides process management for the Node.js application. It keeps the application running in the background, allows it to be restarted, and provides a way to monitor its status.

In this project, PM2 showed the EpicBook process as online.

---

**8. What does idempotency mean in Ansible?**

Idempotency means that running the same Ansible automation repeatedly should not make unnecessary changes when the server is already in the desired state.

For example, once Nginx is installed and configured correctly, running the playbook again should recognize that the configuration is already correct instead of reinstalling or changing everything unnecessarily.

---

**9. What issue did you face during the deployment, and how did you fix it?**

I initially placed the Ansible handlers inside the role task files. Ansible reported a syntax error because handlers belong in a separate handlers/main.yml file.

I fixed the problem by creating separate handler files for the Nginx and EpicBook roles and moving the handler definitions there. After the correction, the playbook passed the syntax check and completed successfully with failed=0.

---

**10. What security improvement would you make before using this setup in production?**

Before using this setup in production, I would improve secret management by using a dedicated secrets-management solution such as AWS Secrets Manager or Ansible Vault instead of relying only on an environment variable.

I would also restrict SSH access, use HTTPS with a valid TLS certificate, apply regular security updates, use stronger production database and EC2 configurations, enable appropriate logging and monitoring, and follow the principle of least privilege for AWS permissions and security groups.
---

# Required Files

Confirm that the following files are included in your GitHub repository or assignment folder:

- [ ] `README.md`
- [ ] Terraform files under either `terraform/azure/` or `terraform/aws/`
- [ ] `ansible/ansible.cfg`
- [ ] `ansible/inventory.ini`
- [ ] `ansible/site.yml`
- [ ] `ansible/group_vars/web.yml`
- [ ] `ansible/roles/common/tasks/main.yml`
- [ ] `ansible/roles/nginx/tasks/main.yml`
- [ ] `ansible/roles/nginx/templates/epicbook.conf.j2`
- [ ] `ansible/roles/epicbook/tasks/main.yml`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots.
- Mention the cloud provider used: Azure or AWS.
- Add the VM public IP address.
- Add the final application URL.
- Add Terraform output proof.
- Add Ansible role tree proof.
- Add all required notes and assignment question answers.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, cloud credentials, database credentials, Terraform state files, subscription IDs, or account IDs.
- Submit only your Google Doc link.

---

# Completion Checklist

- [ ] Task 1: Project folder layout created
- [ ] Task 2: Terraform infrastructure provisioned
- [ ] Task 3: SSH key-based access verified
- [ ] Task 4: Ansible inventory and configuration created
- [ ] Task 5: Main Ansible playbook created
- [ ] Task 6: `common` role created
- [ ] Task 7: `nginx` role created
- [ ] Task 8: `epicbook` role created
- [ ] Task 9: Group variables created
- [ ] Task 10: Ansible playbook run completed
- [ ] Task 11: EpicBook deployment verified
- [ ] Terraform files created under only one cloud provider folder
- [ ] One Ubuntu VM was created
- [ ] One managed MySQL database was created
- [ ] SSH port `22` is restricted to the controller public IP
- [ ] HTTP port `80` is accessible
- [ ] MySQL port `3306` is not publicly open
- [ ] `ansible web -i inventory.ini -m ping` returns `SUCCESS`
- [ ] `site.yml` calls the roles in the correct order
- [ ] Database secrets are hidden or handled securely
- [ ] Nginx is active
- [ ] PM2 shows the EpicBook application running
- [ ] EpicBook responds on port `8080`
- [ ] Public URL loads in the browser
- [ ] Cart API verification works
- [ ] Playbook completes with `failed=0`
- [ ] Screenshots 1–27 are included
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