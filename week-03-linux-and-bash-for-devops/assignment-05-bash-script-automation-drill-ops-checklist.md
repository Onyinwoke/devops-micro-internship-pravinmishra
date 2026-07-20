# Assignment 5 — Bash Script Automation Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will practice Bash scripting by building a series of small automation scripts covering environment setup, variables, arrays, loops, file conditionals, if-else logic, and functions. These scripts form the foundation of real-world Linux automation used in DevOps, cloud, and production support environments.

---

# Task 1 — Bash Environment & Workspace Setup

## Goal

Verify that Bash is available on your system and create a clean workspace for this assignment.

### Evidence

#### Screenshot 1 — Output of `echo $SHELL` and `bash --version`

![Screenshot 1](<Screenshot 2026-07-16 223754.png>)

---

#### Screenshot 2 — Output of `pwd` and `ls -lah` showing the scripts directory

![Screenshot 2](<Screenshot 2026-07-16 223754-1.png>)

---

### Notes

Answer the following in your own words:

**1. What is Bash?**

Bash (Bourne Again Shell) is a command-line interpreter that allows users to interact with the Linux operating system. It can execute commands, automate repetitive tasks, and run shell scripts.

---

**2. What is the difference between shell and Bash?**

A shell is a general program that provides a command-line interface for interacting with an operating system. Bash is one specific type of shell and is one of the most widely used on Linux systems.

---

**3. Why is it important to confirm the Bash version before writing scripts?**

Different Bash versions support different features. Checking the version helps ensure the script will run correctly and avoids compatibility issues.

---

# Task 2 — Your First Bash Script

## Goal

Create your first Bash script, make it executable, and run it from the terminal.

### Evidence

#### Screenshot 1 — Content of `first-script.sh`

![Screenshot 1](<Screenshot 2026-07-16 231500.png>)

---

#### Screenshot 2 — Output of `./first-script.sh`

![Screenshot 2](<Screenshot 2026-07-16 231329.png>)

---

#### Screenshot 3 — Output of `ls -l first-script.sh` showing executable permission

![Screenshot 3](<Screenshot 2026-07-16 231546.png>)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `#!/bin/bash`?**

It tells the operating system to use the Bash interpreter to execute the script.

---

**2. Why do we use `chmod +x` before running a script?**

It gives the script execute permission, allowing it to be run directly from the terminal.

---

**3. What is the difference between running a script using `./script.sh` and `bash script.sh`?**

./script.sh runs the script as an executable and requires execute permission. bash script.sh runs the script through the Bash interpreter and does not require execute permission.

---

# Task 3 — Variables: User Information Script

## Goal

Use variables to store and display user-related information.

### Evidence

#### Screenshot 1 — Content of `user-info.sh`

![Screenshot 1](<Screenshot 2026-07-17 232514.png>)

---

#### Screenshot 2 — Output of `./user-info.sh`

![Screenshot 2](<Screenshot 2026-07-16 231933.png>)

---

### Notes

Answer the following in your own words:

**1. What is a variable in Bash?**

A variable is a named storage location used to hold data such as text or numbers for use within a script.

---

**2. Why should we avoid spaces around the `=` sign when creating variables?**

Spaces make Bash interpret the statement incorrectly, resulting in an error instead of assigning a value.

---

**3. How do you access the value stored inside a Bash variable?**

By placing a dollar sign ($) before the variable name, for example:

---

# Task 4 — Arrays & Loops: Tools Checklist Script

## Goal

Use arrays and loops to print a checklist of tools used in Bash scripting.

### Evidence

#### Screenshot 1 — Content of `tools-checklist.sh`

![Screenshot 1](<Screenshot 2026-07-16 232049.png>)

---

#### Screenshot 2 — Output of `./tools-checklist.sh`

![Screenshot 2](<Screenshot 2026-07-16 232148.png>)

---

### Notes

Answer the following in your own words:

**1. What is an array in Bash?**

An array is a collection of multiple values stored under a single variable name.

---

**2. Why are arrays useful in scripts?**

They allow related data to be stored together, making it easier to process multiple items without creating many separate variables.

---

**3. What does `"${tools[@]}"` mean?**

It represents all the elements stored in the tools array and allows the loop to process each item.

---

**4. What is the purpose of the `for` loop in this script?**

The for loop goes through each item in the array and performs the same action on every element.

---

# Task 5 — Loops: Number Counter Script

## Goal

Use loops to repeat a task multiple times.

### Evidence

#### Screenshot 1 — Content of `counter.sh`

![Screenshot 1](<Screenshot 2026-07-16 232245.png>)

---

#### Screenshot 2 — Output of `./counter.sh`

![Screenshot 2](<Screenshot 2026-07-16 232320.png>)

---

### Notes

Answer the following in your own words:

**1. What is a loop?**

A loop is a programming structure that repeats a set of commands until a condition is met.

---

**2. Why do we use loops in Bash scripting?**

Loops automate repetitive tasks, reducing manual work and making scripts shorter and easier to maintain.

---

**3. How many times did the loop run in your script?**

It ran 5 times.

---

**4. What would you change if you wanted the loop to run 10 times?**

I would change the loop range from {1..5} to {1..10}

---

# Task 6 — Files & Conditionals: File Validation Script

## Goal

Use file checks and conditionals to verify whether files and directories exist.

### Evidence

#### Screenshot 1 — Output of `ls -lah ../test-folder`

![Screenshot 1](<Screenshot 2026-07-16 232710.png>)

---

#### Screenshot 2 — Content of `file-check.sh`

![Screenshot 2](<Screenshot 2026-07-16 233105.png>)

---

#### Screenshot 3 — Output of `./file-check.sh`

![Screenshot 3](<Screenshot 2026-07-16 233105-1.png>)

---

### Notes

Answer the following in your own words:

**1. What does `-d` check in Bash?**

It checks whether the specified path is an existing directory.

---

**2. What does `-f` check in Bash?**

It checks whether the specified path is an existing regular file.

---

**3. Why should file and directory paths be stored in variables?**

Using variables makes scripts easier to update, improves readability, and avoids repeating the same path multiple times.

---

**4. What happens if the file does not exist?**

The condition evaluates to false, and the script executes the alternative action, such as displaying an error message.

---

# Task 7 — Conditionals: Pass or Retry Script

## Goal

Use if-else conditionals to make decisions based on a variable value.

### Evidence

#### Screenshot 1 — Content of `score-check.sh` with `score=85`

![Screenshot 1](<Screenshot 2026-07-16 233415.png>)

---

#### Screenshot 2 — Output showing `Result: Pass`

![Screenshot 2](<Screenshot 2026-07-16 233458.png>)

---

#### Screenshot 3 — Content of `score-check.sh` with `score=55`

![Screenshot 3](<Screenshot 2026-07-17 235438.png>)

---

#### Screenshot 4 — Output showing `Result: Retry`

![Screenshot 4](<Screenshot 2026-07-16 233603.png>)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of if-else in Bash?**

It allows a script to make decisions by running different commands based on whether a condition is true or false.

---

**2. What does `-ge` mean?**

-ge means greater than or equal to when comparing numbers.

---

**3. Why should conditions be tested with different values?**

Testing different values confirms that every branch of the script works correctly and handles different scenarios.

---

**4. How can conditionals help in automation scripts?**


They allow scripts to respond automatically to different situations, making automation more flexible and reliable.

---

# Task 8 — Functions: Final Bash Automation Script

## Goal

Create a final Bash script using functions to organize reusable code.

### Evidence

#### Screenshot 1 — Content of `final-automation.sh`

![Screenshot 1](<Screenshot 2026-07-16 233927.png>)

---

#### Screenshot 2 — Output of `./final-automation.sh`

![Screenshot 2](<Screenshot 2026-07-16 234435.png>)

---

#### Screenshot 3 — Output of `ls -lah` showing all created scripts

![Screenshot 3](<Screenshot 2026-07-16 234548.png>)

---

### Notes

Answer the following in your own words:

**1. What is a function in Bash?**

A function is a reusable block of code that performs a specific task whenever it is called.

---

**2. Why are functions useful in scripts?**

Functions reduce repeated code, improve readability, and make scripts easier to maintain and update.

---

**3. Which functions did you create in this script?**

I created separate functions to organize different tasks, such as displaying user information, checking files, printing the tools checklist, and showing the final automation summary.

---

**4. How does this final script combine variables, arrays, loops, conditionals, files, and functions?**

The script uses variables to store information, arrays to manage multiple items, loops to process them, conditionals to make decisions based on different scenarios, file checks to validate paths, and functions to organize the code into reusable sections. Together, these features create a structured and efficient automation script.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/nwoke-onyinye_devops-bash-linux-share-7484023404476637184-9fzb/?highlightedUpdateUrn=urn%3Ali%3Aactivity%3A7484023406011658240&highlightedUpdateType=SOCIAL_SHARE&origin=SOCIAL_SHARE&utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c

---

#### Screenshot — Published LinkedIn post

![LinkedIn Post](<Screenshot 2026-07-20 124030.png>)

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- All script files must be created and run successfully
- Required notes must be answered clearly for every task
- Do not expose sensitive information (keys, passwords, credentials)

---

# Completion Checklist

- [ ] Task 1: Environment setup verified, workspace created (Screenshots 1–2, Notes answered)
- [ ] Task 2: First script created, executed, permissions verified (Screenshots 1–3, Notes answered)
- [ ] Task 3: Variables script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 4: Arrays and loops script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 5: Counter loop script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 6: File validation script created and run (Screenshots 1–3, Notes answered)
- [ ] Task 7: Pass/Retry conditional script tested with both values (Screenshots 1–4, Notes answered)
- [ ] Task 8: Final automation script created and run (Screenshots 1–3, Notes answered)
- [ ] All scripts run without errors
- [ ] Full Name visible in all required screenshots
- [ ] LinkedIn post published and URL submitted
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