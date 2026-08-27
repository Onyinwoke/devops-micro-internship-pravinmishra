# Assignment 7 — AI-Assisted AWS Security and Cost Audit

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build a read-only Bash script that audits the AWS resources you deployed earlier this week — your S3 static site, EC2 instance(s), security groups, RDS database, and EBS volumes — for common security and cost misconfigurations.

You will then connect that script to Claude Code as a reusable `/aws-audit` skill that explains what it found and recommends a fix, without ever making the fix itself.

Finally, you will find a real misconfiguration in your own account, apply the fix yourself, and prove it worked with a second audit run.

---

# Task 1 — Confirm Your AWS Resources and Set Up Your Workspace

## Goal

Confirm your AWS CLI is authenticated and can see the S3 bucket, EC2 instance(s), and RDS instance you built earlier this week, then create a workspace folder for this assignment.

### Evidence

#### Screenshot 1 — Output of `aws s3 ls`, the EC2 instance table, and the RDS instance table (blur the Account ID if visible)

![Screesnhot 1](<Screenshot 2026-08-27 104848.png>)

---

#### Screenshot 2 — Output of `pwd` and `find . -maxdepth 4 -type d | sort`

![Screesnhot 2](<Screenshot 2026-08-27 112059.png>)

---

### Notes You Must Write (Very Important)

**1. Which resources from this week's earlier assignments did you see in the listings?**

The S3 bucket 'data-ingestion-platform-onyinye-2026' was visible in the AWS CLI output, confirming it exists in the account. The RDS instance 'my-db-instance' was also confirmed to exist in the region. However, there were no EC2 instances currently running in the output, because I terminated since the earlier assignments.

**2. Why must you confirm your resources exist before writing an audit script against them?**

You must confirm the resources exist so the audit script has valid resources to query. This prevents errors caused by incorrect resource names, IDs, regions, or missing infrastructure, and ensures the audit results accurately reflect the resources you actually deployed. Without confirmation, the script could fail with resource not found errors or produce misleading results.

---

# Task 2 — Define Safety Rules in CLAUDE.md

## Goal

Create a `CLAUDE.md` in your workspace that tells Claude the audit script is read-only, that it must never run a command that creates, modifies, or deletes an AWS resource, and that any remediation must be recommended, never executed automatically.

### Evidence

#### Screenshot 3 — `CLAUDE.md` open in VS Code showing all four sections

![Screenshot 3](<Screenshot 2026-08-27 112855.png>)

---

### Notes You Must Write (Very Important)

**1. Why should Claude never be given permission to run `revoke-security-group-ingress` itself, even if the fix is obviously correct?**

The Agentic Loop requires human approval before any account modifications are made. Even if the finding is clearly correct, allowing Claude to execute remediation directly bypasses the critical "Ask the human to approve and execute" step. The human must understand what change is being made and explicitly run the command themselves to maintain accountability, auditability, and control over their own infrastructure. This separation ensures no mistakes cascade without human review.

**2. Which rule prevents Claude from claiming a finding that the report does not support?**

The rule "Do not claim a finding unless the report contains supporting evidence" prevents Claude from inferring or hallucinating findings. Every finding claimed in the analysis must be backed by actual data from the Bash audit report. This prevents false positives that could lead to unnecessary remediation work or false negatives that hide real security issues.

---

# Task 3 — Plan the Audit with Claude Code

## Goal

Ask Claude Code to propose a read-only audit plan covering five checks — S3 public-access settings, security groups open to the whole internet on SSH and MySQL ports, RDS public accessibility, and EBS volume encryption — without creating or editing any file yet.

### Evidence

#### Screenshot 4 — Claude Code showing the five-check plan

![Screenshot 4](<Screenshot 2026-08-27 142523.png>)

---

### Notes You Must Write (Very Important)

**1. Which part of this task represents the Gather phase?**

The Gather phase is represented by the five AWS CLI commands that Claude proposed to collect evidence:

aws s3api get-bucket-public-access-block - Gather S3 public access configuration
aws ec2 describe-security-groups - Gather security group rules
aws ec2 describe-security-group-rules (filtered) - Gather specific ingress rules
aws rds describe-db-instances - Gather RDS public accessibility settings
aws ec2 describe-volumes - Gather EBS encryption status

**2. Did every proposed command start with `describe-`, `get-`, or `list-`? Why does that matter?**

Yes, every command started with describe-, get-, or list-, which are all read-only operations in AWS CLI. This matters because it guarantees the script cannot accidentally create, modify, or delete resources. If Claude ever proposed a command like create-, terminate-, revoke-, or authorize-, it would violate the safety rules and put the account at risk.

---

# Task 4 — Build the AWS Audit Script

## Goal

Write a Bash script that runs the five checks from Task 3 using only read-only AWS CLI calls, writes a PASS/WARN/FAIL report to a file, and exits with a different code depending on the overall result.

Make it executable and confirm it has no syntax errors.

### Evidence

#### Screenshot 5 — Top section of `aws-audit.sh` showing the variables and the checks array

![Screenshot 5](<Screenshot 2026-08-27 120128.png>)

---

#### Screenshot 6 — One check function (for example `check_ssh_open_to_world`) showing the AWS CLI call and conditional

![Screenshot 6](<Screenshot 2026-08-27 121303.png>)

---

#### Screenshot 7 — Output of `bash -n scripts/aws-audit.sh` and `ls -l scripts/aws-audit.sh`

![Screenshot 7](<Screenshot 2026-08-27 121506.png>)

---

### Notes You Must Write (Very Important)

**1. What is stored in the checks array, and how does the loop use it?**

The checks array stores the names of five check functions:
checks=(
  check_s3_public_access
  check_ssh_open_to_world
  check_mysql_open_to_world
  check_rds_public_access
  check_ebs_encryption
)
The loop iterates through this array, calling each function by name. Each function executes its AWS CLI query, parses the results, and calls either mark_pass() or mark_failure() to record the result. This design makes it easy to add or remove checks without rewriting the main loop.

**2. Why does every AWS CLI call in this script use `--query` and `--output text` instead of parsing raw JSON?**

--query filters the JSON output to only the fields we need, and --output text converts it to plain text that Bash can easily parse with grep, awk, or simple conditionals. Parsing raw JSON in Bash is error-prone and requires jq or similar tools. By using AWS CLI's built-in query language, the script becomes simpler, faster, and less dependent on external tools. It also makes the script output more readable for humans.

**3. Why does the script use different exit codes for HEALTHY, WARN, and FAIL?**

Different exit codes allow external tools, scripts, or CI/CD pipelines to take different actions based on severity:

Exit code 0 (HEALTHY) = Everything is secure, no action needed
Exit code 1 (WARN) = Issues found but not critical, review recommended
Exit code 2 (FAIL) = Critical security or cost issues, immediate action recommended

This enables automation: a CI/CD pipeline could fail a deployment on FAIL, notify the team on WARN, or proceed on HEALTHY.

---

# Task 5 — Run the Baseline Audit

## Goal

Run the script against your live AWS account and capture the current state before making any changes.

### Evidence

#### Screenshot 8 — Output of `./scripts/aws-audit.sh` showing your Full Name and all five checks

![Screenshot 8](<Screenshot 2026-08-27 121612.png>)

---

#### Screenshot 9 — Output showing the captured exit code and final summary

![Screenshot 9](<Screenshot 2026-08-27 121612-1.png>)

---

### Notes You Must Write (Very Important)

**1. What is the overall status of your baseline audit?**

The baseline audit status is Mostly secure with 1 warning requiring attention. The script ran successfully and completed all five checks, producing a detailed report with timestamp and resource identifiers

**2. Did any check return FAIL or WARN? If so, which one, and what evidence did it show?**

Yes, two findings returned WARN status:

[WARN] RDS Public Accessibility Cannot Be Verified - Evidence: "Could not determine public accessibility for RDS instance 'my-db-instance'"
[WARN] 1 EBS volume(s) are not encrypted - Evidence: At least one EBS volume in the region lacks encryption at rest.
Both of these are warnings rather than hard failures because they indicate security issues but didn't return a definitive FAIL state.

**3. If every check passed, what does that tell you about the security posture of your account so far?**

The account achieved 3 PASS results:

✓ S3 bucket 'data-ingestion-platform-onyinye-2026' blocks and ignores public ACLs
✓ No security group rule allows SSH (port 22) from 0.0.0.0/0
✓ No security group rule allows MySQL (port 3306) from 0.0.0.0/0

---

# Task 6 — Build and Run the /aws-audit Skill

## Goal

Turn the script into a Claude Code skill named `/aws-audit` that runs the script, reads the report, and explains every finding along with its estimated cost or security risk — with tool access restricted so it can never modify your AWS account.

### Evidence

#### Screenshot 10 — `SKILL.md` showing the frontmatter, tool restrictions, and safety rules

![Screnshot 10](<Screenshot 2026-08-27 122825.png>)

---

#### Screenshot 11 — `/aws-audit` output showing findings, cost/risk impact, and a recommended remediation command (or a clean report if your baseline passed everything)

![Screenshot 11](<Screenshot 2026-08-27 120714.png>)
![Screenshot 11.1](<Screenshot 2026-08-27 120726.png>)

---

### Notes You Must Write (Very Important)

**1. Why does this skill have Bash, Read, and Grep, but not Write?**

The skill is explicitly read-only. Bash is used to execute the audit script, Read is used to access the generated report, and Grep is used to parse specific findings. Write access is intentionally excluded to prevent Claude from creating, modifying, or deleting any files. This constraint ensures the skill can only observe and analyze, never alter the audit workflow or account state.

**2. What part is performed by Bash, and what part is performed by Claude?**

Bash performs: Gathering evidence by executing the audit script with AWS CLI calls, then generating the raw PASS/WARN/FAIL report.

Claude performs: Reading the report, interpreting each finding, estimating the cost and security risk impact, formulating clear explanations of what went wrong and why it matters, and recommending specific remediation commands for the human to review and execute.

**3. Why is estimating cost/risk impact something the AI adds on top of a plain PASS/FAIL script?**

Bash script is a simple binary auditor—it only knows PASS/FAIL. It doesn't know the business context. Claude adds human-level reasoning: explaining why each finding matters, quantifying the potential blast radius (e.g., "account compromise, data breach, uncontrolled data transfer costs"), and prioritizing which findings deserve immediate attention. This transforms a bare list of violations into actionable intelligence.

---

# Task 7 — Fix a Real Finding and Re-Verify

## Goal

Pick one real finding from your baseline report (or deliberately open a security group rule if your baseline was fully clean), apply the fix yourself in a separate terminal — scoped to your own IP address, not the whole internet — then rerun the script to prove the finding is resolved.

### Evidence

#### Screenshot 12 — Output of the `revoke-security-group-ingress` and `authorize-security-group-ingress` commands you ran yourself

![Screenshot 12](<Screenshot 2026-08-27 140556.png>)
![Screenshot 12.1](<Screenshot 2026-08-27 140611.png>)

---

#### Screenshot 13 — Rerun of `./scripts/aws-audit.sh` showing the finding is now PASS

![Screenshot 13](<Screenshot 2026-08-27 140817.png>)

---

### Notes You Must Write (Very Important)

**1. Which exact finding did you fix, and what command did you run?**

I fixed the SSH port 22 open to the internet (0.0.0.0/0) finding in a security group that was not initially caught as FAIL in the baseline. I ran two commands:

Revoke the open rule:

bash
aws ec2 revoke-security-group-ingress \
  --group-id sg-0daebf2f0315bc61c \
  --protocol tcp \
  --port 22 \
  --cidr 0.0.0.0/0

Authorize a scoped rule to my IP:

bash
aws ec2 authorize-security-group-ingress \
  --group-id sg-0daebf2f0315bc61c \
  --protocol tcp \
  --port 22 \
  --cidr 102.88.169.34/32

This replaced the dangerous 0.0.0.0/0 access with my specific IP address (102.88.169.34/32).

**2. Why did you scope the new rule to your own IP address instead of leaving it open to `0.0.0.0/0`?**

Scoping to a single IP (your own) follows the principle of least privilege: only grant the minimum access necessary to do the job. This dramatically reduces the attack surface—instead of allowing anyone on the internet to attempt SSH attacks, only your trusted IP can connect. If you need to access the instance from a different location, you can update the rule with that IP instead of reverting to universal access.

**3. Did Claude execute the remediation command, or did you? Why does that matter?**

I executed the remediation commands myself using the AWS CLI in the terminal. Claude only explained what needed to be fixed and recommended the exact commands, but it never ran them. This matters because:

It ensures human accountability: you understand and approve every change
It maintains the Agentic Loop discipline: evidence gathering, analysis, and remediation are separate phases
It prevents accidental cascading changes if Claude misinterpreted a finding
It gives you the chance to review the commands before running them

**4. Which phase of the Agentic Loop does the Bash script represent? Which phase does Claude's explanation represent? Which phase is you running the fix?**

Bash script = Gather phase: Passively observes infrastructure and collects evidence via read-only AWS CLI calls
Claude's explanation = Analyze & Recommend phase: Interprets evidence, estimates impact, explains findings, and recommends actions
You running the fix = Execute & Verify phase: Approves and runs the remediation command, then re-runs the audit to confirm the fix worked

This is the complete Agentic Loop: humans stay in control of decisions and execution, while AI handles evidence gathering and analysis.

---

# LinkedIn Post (Required)

## Goal

Create a LinkedIn post including:

- What you built: a read-only AWS audit script and a Claude Code `/aws-audit` skill
- One real finding you caught and fixed in your own account
- What the workflow demonstrated: evidence gathering, AI-assisted cost/risk analysis, human-approved remediation, and reverification
- Screenshot of the finding before the fix
- Screenshot of the same check passing after the fix
- Write 4–6 lines in your own words

Suggested tags:

`#DMIByPravinMishra #AWS #AgenticAI #ClaudeCode #DevOps`

### Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/erREktRA

---

#### Screenshot of Published LinkedIn Post

![LinkedIn Post](<Screenshot 2026-08-27 152313.png>)

---

# Submission Instructions

Complete all tasks in sequence.

Your submission must include:

- All 13 required task screenshots
- Answers to every **Notes You Must Write** question
- `CLAUDE.md`
- `scripts/aws-audit.sh`
- `.claude/skills/aws-audit/SKILL.md`
- `reports/aws-audit-report.txt` baseline report and the reverified report from Task 7
- GitHub folder or repository URL containing the assignment files
- Your Full Name visible in the required outputs
- LinkedIn post URL
- Screenshot of the published LinkedIn post

Submit only a Google Doc link.

Add the GitHub URL inside the Google Doc.

Follow the Assignment Submission Guidelines.

---

# Completion Checklist

- [ ] Task 1: AWS resources confirmed and workspace created (Screenshots 1–2)
- [ ] Task 2: `CLAUDE.md` created with project context and safety rules (Screenshot 3)
- [ ] Task 3: Claude produced a read-only five-check audit plan before any script existed (Screenshot 4)
- [ ] Task 4: `aws-audit.sh` built, executable, and passes `bash -n` (Screenshots 5–7)
- [ ] Task 5: Baseline audit captured and saved with Full Name visible (Screenshots 8–9)
- [ ] Task 6: `/aws-audit` skill loads and runs successfully with no Write permission (Screenshots 10–11)
- [ ] Task 7: A real finding was fixed by you and reverified as PASS (Screenshots 12–13)
- [ ] Skill never executed a remediation command
- [ ] New security group rule is scoped to your own IP, not `0.0.0.0/0`
- [ ] All 13 required task screenshots are included
- [ ] All "Notes You Must Write" questions are answered in your own words
- [ ] No AWS credentials or unblurred account IDs exposed
- [ ] LinkedIn post published and URL submitted
- [ ] GitHub URL included in the Google Doc
- [ ] Google Doc is accessible
- [ ] Link tested in incognito mode

---

# Final Submission

Submit only your Google Doc link.

### Question

Based on the instructions and tasks above, submit your completed document with all required explanations, screenshots, reports, script file, skill file, and GitHub URL.

https://docs.google.com/document/d/1lM0tMReS4ZVEYZdczmXj6KO-ZQFvusAWIqvFj5K0tlY/edit?tab=t.0

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