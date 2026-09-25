# Assignment 6 — AI-Assisted Ansible Change Risk Review

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build an AI-assisted Ansible risk-review workflow using `ansible-playbook --check --diff`, Bash scripting, and Claude Code.

You will review possible server changes before applying them, classify risky tasks, and keep the final apply decision under human control.

---

# Task 1 — Confirm EpicBook Connectivity and Create the Workspace

## Goal

Confirm that your previous EpicBook Ansible project is working before creating the risk-review automation.

### Evidence

#### Screenshot 1 — Output of `ansible web -i inventory.ini -m ping`

![Screenshot 1](<Screenshot 2026-09-25 130653.png>)

---

#### Screenshot 2 — Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`

![Screenshot 2](<Screenshot 2026-09-25 130741.png>)

---

#### Screenshot 3 — Output of `pwd` and `find . -maxdepth 4 -type d | sort`

![Screenshot 3](<Screenshot 2026-09-25 131030.png>)
---

### Notes

Answer the following in your own words:

**1. What proves that Ansible can reach your EpicBook VM?**

The successful ansible web -i inventory.ini -m ping command proves that Ansible can connect to the EpicBook VM. The response showed SUCCESS and "ping": "pong", confirming that the server is reachable and Ansible can communicate with it.

---

**2. Why should you confirm playbook syntax before building a risk-review script?**

Checking the playbook syntax first confirms that the playbook is correctly written and can be parsed by Ansible. This prevents errors in the playbook itself from being mistaken for risk-review results.

---

# Task 2 — Create Project Context and Safety Rules in CLAUDE.md

## Goal

Create a `CLAUDE.md` file that tells Claude Code how this project must behave.

### Evidence

#### Screenshot 4 — `CLAUDE.md` open in VS Code or terminal showing the safety rules

![Screenshot 4](<Screenshot 2026-09-25 131544.png>)

---

### Notes

Answer the following in your own words:

**1. Why should Claude Code have project-specific safety rules?**

Project-specific safety rules clearly define what Claude is allowed and not allowed to do. They keep Claude focused on reviewing changes without accidentally modifying the infrastructure or sensitive project files.

---

**2. Why should the human run the real Ansible playbook manually?**

The human should make the final decision because applying changes can affect the server and application. Manual execution gives the human an opportunity to review the risks before anything is actually changed.

---

**3. Which rule prevents Claude Code from applying changes automatically?**

The rule “Never apply, converge, or fix the playbook automatically” prevents Claude Code from applying changes.

---

# Task 3 — Ask Claude Code to Plan the Risk Review

## Goal

Use Claude Code to produce a read-only plan before writing the Bash script.

### Evidence

#### Screenshot 5 — Claude Code showing the four-category risk-classification plan

![Screenshot 5](<Screenshot 2026-09-25 132040.png>)
![Screenshot 5.1](<Screenshot 2026-09-25 132057.png>)
![Screenshot 5.2](<Screenshot 2026-09-25 132119.png>)
![Screenshot 5.3](<Screenshot 2026-09-25 132128.png>)

---

### Notes

Answer the following in your own words:

**1. Which part of this task represents the Gather phase?**

The Gather phase is running ansible-playbook --check --diff and capturing its output. This collects evidence about what Ansible would change without actually applying those changes.

---

**2. Which part represents the Analyze phase?**

The Analyze phase is parsing the dry-run output, identifying changed tasks, and classifying them into the four risk categories based on their task names and modules.

---

**3. How did you verify Claude Code did not create or edit files?**

I verified this by explicitly instructing Claude Code not to create or edit any files and checking its response to confirm that it only proposed a plan. The task did not require Claude to make any file changes.

---

# Task 4 — Build the Ansible Risk Review Script

## Goal

Create a Bash script that runs an Ansible dry run and classifies risky changes.

### Evidence

#### Screenshot 6 — Top section of `ansible-check-review.sh` showing `full_name`, `playbook_path`, `inventory_path`, and the `checks` array

![Screenshot 6](<Screenshot 2026-09-25 133113.png>)

---

#### Screenshot 7 — Middle section showing `extract_changed_tasks` and `check_tasks_matching_pattern`

![Screenshot 7](<Screenshot 2026-09-25 134346.png>)
![Screenshot 7](<Screenshot 2026-09-25 134328.png>)

---

#### Screenshot 8 — Bottom section showing the loop, summary, and exit behavior

![Screenshot 8](<Screenshot 2026-09-25 134428.png>)
![Screenshot 8](<Screenshot 2026-09-25 134419.png>)

---

#### Screenshot 9 — Output of `bash -n ansible-check-review.sh` and `ls -l ansible-check-review.sh`

![Screenshot 9](<Screenshot 2026-09-25 135124.png>)

---

### Notes

Answer the following in your own words:

**1. What is stored in the `changed_tasks` array?**

The changed_tasks array stores the names of Ansible tasks that the dry run reports as changes that would be made to the server

---

**2. Which function finds changed tasks from the Ansible output?**

The extract_changed_tasks function searches the Ansible output and extracts the task names that are reported as changed.

---

**3. Why does the script use `--check --diff`?**

--check lets Ansible simulate the playbook without applying the changes, while --diff shows the differences that would be made. Together, they provide evidence for reviewing changes safely before applying them.

---

**4. Why does the script use different exit codes for healthy, warning, and failed results?**

Different exit codes make it easy to understand the result and for other tools or scripts to respond appropriately. 0 means no changes were detected, 1 means changes were detected and should be reviewed, and 2 indicates a failure or risky condition requiring attention.

---

# Task 5 — Run the Baseline Dry-Run Review

## Goal

Run the script against your current EpicBook playbook and confirm the baseline risk status.

### Evidence

#### Screenshot 10 — Output of `./ansible-check-review.sh`

![Screenshot 10](<Screenshot 2026-09-25 135003.png>)

---

#### Screenshot 11 — Output of `echo "Captured Exit Code: $script_exit_code"` and `cat reports/ansible-risk-report.txt`

![Screenshot 11](<Screenshot 2026-09-25 135625.png>)
![Screenshot 11.1](<Screenshot 2026-09-25 135727.png>)

---

### Notes

Answer the following in your own words:

**1. What was the overall status of your baseline run?**

The overall status was FAIL because the Ansible dry run reported one failed task.

---

**2. Did any tasks report `changed`?**

Yes. Four tasks were reported as changed: updating the APT cache, installing common packages, installing Nginx, and deploying the EpicBook Nginx configuration.

---

**3. Were any changed tasks flagged as risky?**

No. None of the four changed tasks matched the script's four defined risk categories: service restarts, firewall changes, user/sudo changes, or removal changes.

---

**4. What does the script exit code mean?**

The exit code was 2, which means the review detected a failure condition. In this run, Ansible reported failed=1, so the result requires investigation before applying changes.

---

# Task 6 — Create and Run the Claude Code Skill

## Goal

Turn the Bash script into a reusable Claude Code skill called `/ansible-risk-review`.

### Evidence

#### Screenshot 12 — `SKILL.md` showing the frontmatter, allowed tools, and safety rules

![Screenshot 12](<Screenshot 2026-09-25 140020.png>)
![Screenshot 12.1](<Screenshot 2026-09-25 140117.png>)

---

#### Screenshot 13 — Claude Code output after running `/ansible-risk-review`

![Screenshot 13](<Screenshot 2026-09-25 142313.png>) 
![Screenshot 13](<Screenshot 2026-09-25 142324.png>) 
---

### Notes

Answer the following in your own words:

**1. Why does this skill allow `Bash`, `Read`, and `Grep`?**

These tools give Claude enough access to run the read-only review script, read the generated reports, and search through the report for relevant information.

---

**2. Why does this skill not allow file editing?**

File editing is disabled to prevent Claude from accidentally changing the playbook, roles, inventory, infrastructure, or other project files while performing the risk review.

---

**3. What part is handled by Bash?**

Bash handles the evidence gathering. It runs the ansible-check-review.sh script, performs the --check --diff dry run, and generates the risk reports.

---

**4. What part is handled by Claude Code?**

Claude Code handles the analysis. It reads the reports, identifies changed or risky tasks, explains their potential impact, and provides a recommendation for human review.

---

**5. Why is this better than asking Claude Code if the playbook is safe without giving it evidence?**

The approach is better because Claude bases its analysis on actual Ansible dry-run results rather than making a judgment from the playbook alone. This provides concrete evidence of what Ansible would change before anything is applied.

---

# Task 7 — Introduce a Controlled Risky Change and Let the Skill Catch It

## Goal

Add a small controlled risky change in your lab playbook and confirm the script and Claude Code catch it before applying.

### Evidence

#### Screenshot 14 — The added risky task inside the role file

![Screenshot 14](<Screenshot 2026-09-25 143116.png>)

---

#### Screenshot 15 — Output of `./ansible-check-review.sh`

![Screenshot 15](<Screenshot 2026-09-25 143116.png>)

---

#### Screenshot 16 — Claude Code `/ansible-risk-review` output showing the risky finding

![Screenshot 16](<Screenshot 2026-09-25 143116.png>)

---

#### Screenshot 17 — Output of `cat reports/risky-change-report.txt`

![Screenshot 17](<Screenshot 2026-09-25 143607.png>)

---

### Notes

Answer the following in your own words:

**1. Which risk category did the added task fall into?**

Package or file removal.

---

**2. What evidence proves the task would change something?**

The --check --diff report detected “Remove temporary EpicBook risk test file” as a changed task and flagged it as a risky removal.
---

**3. Did Claude Code apply the playbook?**

No. Claude Code only analyzed the risk and did not apply the playbook.

---

**4. Why is it important that Claude Code only analyzed the risk?**

Because applying changes automatically could delete files or affect the server without human approval. Human review keeps the final decision under human control.

---

**5. Which phase of the Agentic Loop is represented by the Bash report?**

Gather — the Bash script collected evidence about what the playbook would change.

---

# Task 8 — Apply as the Human, Verify, and Write the Change Summary

## Goal

Review the risky-change report, apply the playbook manually as the human operator, and verify the result.

### Evidence

#### Screenshot 18 — Output of the real playbook run showing the final recap with `failed=0`

 ![Screenshot 18](<Screenshot 2026-09-25 144438.png>)
![Screenshot 18](<Screenshot 2026-09-25 144450.png>)

---

#### Screenshot 19 — Output of `ansible web -i inventory.ini -m ping`

![Screenshot 19](<Screenshot 2026-09-25 144601.png>)

---

#### Screenshot 20 — Second `/ansible-risk-review` output after applying the change

![Screenshot20](<Screenshot 2026-09-25 144905.png>)

---

#### Screenshot 21 — Output of `ls -lah reports`

![Screenshot 21](<Screenshot 2026-09-25 145013.png>)

---

#### Screenshot 22 — `change-summary.md` showing all required sections and your Full Name

![Screenshot 22](<Screenshot 2026-09-25 145148.png>)
![Screenshot 22](<Screenshot 2026-09-25 145204.png>)
---

### Notes

Answer the following in your own words:

**1. What command did you run to apply the change for real?**

ansible-playbook -i inventory.ini site.yml

---

**2. Who made the final decision to apply the playbook?**

I, the human operator, made the final decision after reviewing the risk report.

---

**3. What evidence proves the VM is still reachable?**

The Ansible ping returned SUCCESS with "ping": "pong" and unreachable=0.

---

**4. Why should the risk review be run again after applying?**

To confirm the server's state after the changes and identify any remaining changes that may need review.
---

**5. What could go wrong if an AI agent applied Ansible changes automatically?**

It could make an unintended change, delete files, restart services, or affect the server without human review and approval.

---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`https://www.linkedin.com/posts/nwoke-onyinye_devops-ansible-aws-share-7509250257680035840-lMOW/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c`

---

#### Screenshot — Published LinkedIn post

![LinkedIn Post](<Screenshot 2026-09-25 150018.png>)

---

# Required Files

Confirm that the following files are included in your GitHub repository or assignment folder:

- [ ] `CLAUDE.md`
- [ ] `ansible-check-review.sh`
- [ ] `.claude/skills/ansible-risk-review/SKILL.md`
- [ ] `reports/risky-change-report.txt`
- [ ] `reports/post-apply-report.txt`
- [ ] `change-summary.md`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots and reports.
- All required notes must be answered clearly.
- Do not expose SSH private keys, passwords, cloud credentials, database credentials, or secret environment variables.
- Add your GitHub repository or folder URL inside this document.
- Submit only your Google Doc link.

---

# Completion Checklist

- [ ] Task 1: EpicBook connectivity confirmed and workspace created
- [ ] Task 2: `CLAUDE.md` created with safety rules
- [ ] Task 3: Claude Code produced a read-only risk-review plan
- [ ] Task 4: `ansible-check-review.sh` created and syntax checked
- [ ] Task 5: Baseline dry-run review completed
- [ ] Task 6: Claude Code `/ansible-risk-review` skill created and tested
- [ ] Task 7: Controlled risky change introduced and detected
- [ ] Task 8: Human applied the change and verified the result
- [ ] Risky-change report saved
- [ ] Post-apply report saved
- [ ] Change summary completed
- [ ] All screenshots added
- [ ] All notes answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information exposed
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