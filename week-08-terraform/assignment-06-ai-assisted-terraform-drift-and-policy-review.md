# Assignment 6 — AI-Assisted Terraform Drift and Policy Review

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Student Details

**Full Name:** Onyinye Nwoke  
**GitHub Repository/Folder URL:** https://github.com/Onyinwoke

---

## Purpose

Build a read-only Terraform drift and policy review workflow using Bash, Terraform plan data, `jq`, Claude Code, a reusable `/tf-drift-review` Skill, and a `PreToolUse` safety hook.

The workflow must follow this pattern:

```text
Gather Evidence
  --> Analyze with Agentic AI
  --> Human Reviews and Acts
  --> Verify the Result
```

The `/tf-drift-review` Skill and `tf-drift-check.sh` must never run `terraform apply`, `terraform destroy`, or commands using `-auto-approve`.

---

# Task 1 — Confirm the Clean Baseline and Create the Workspace

## Goal

Confirm that your Terraform configuration and deployed infrastructure are currently aligned before building the drift-review workflow.

## Evidence

### Screenshot 1 — Clean Terraform Plan

Add a screenshot of `terraform plan` showing no pending changes.

![Screenshot 1](<Screenshot 2026-09-21 012201.png>)

---

### Screenshot 2 — Assignment Workspace

Add a screenshot of the folder structure showing `AI Assignment/`, `reports/`, and the Terraform project.

![Screesnhot 2](<Screenshot 2026-09-21 012900.png>)

## Questions

### 1. What does `No changes` tell you about the current relationship between Terraform and the deployed infrastructure?

No changes means Terraform's configuration is currently in sync with the deployed AWS infrastructure, so Terraform has detected no drift or pending changes.

### 2. Why is a clean baseline important before introducing a test change?

A clean baseline provides a known starting point. This allows us to confidently identify and understand any changes detected after we deliberately introduce a test difference

---

# Task 2 — Create Project Context and Safety Rules in `CLAUDE.md`

## Goal

Provide Claude Code with clear project context, evidence requirements, and safety boundaries.

## Evidence

### Screenshot 3 — Project Context and Safety Rules

Add a screenshot of `CLAUDE.md` open in VS Code showing the Project Overview, Review Workflow, Safety Rules, and Output Rules.

![Screenshot 3](<Screenshot 2026-09-21 013348.png>)

## Questions

### 1. Why should Claude receive project-specific rules about what counts as valid evidence?

Project-specific rules tell Claude what evidence to check and what it is allowed to conclude from that evidence.

### 2. Why must the human remain responsible for running `terraform apply`?

The human must remain responsible because terraform apply changes real infrastructure, so a person should review the evidence and approve the change before it happens

### 3. Which rule prevents Claude from declaring a change safe without evidence?

The rule is: “AI should analyze the plan and policy checks and report risks clearly.”
This rule prevent Claude from declaring a change safe without first checking the Terraform plan and policy evidence.

---

# Task 3 — Build the Terraform Drift and Policy Check Script

## Goal

Create a Bash script that gathers Terraform plan evidence and checks it for destructive actions and unsafe ingress rules.

## Evidence

### Screenshot 4 — Script Variables and Checks Array

Add a screenshot of the top section of `tf-drift-check.sh` showing the variables and `checks` array.

![Screenshot 4](<Screenshot 2026-09-21 022012.png>)

---

### Screenshot 5 — Destructive-Action and Open-Ingress Checks

Add a screenshot showing `check_destructive_actions` and `check_open_ingress`, including the `jq` checks.

![Screenshot 5](<Screenshot 2026-09-21 022331.png>)
![Screenshot 5.1](<Screenshot 2026-09-21 022600.png>)

---

### Screenshot 6 — Script Validation and Permissions

Add a screenshot showing successful `bash -n` and `ls -l` output.

![Screenshot 6](<Screenshot 2026-09-21 022754.png>)

## Questions

### 1. What does `terraform plan -detailed-exitcode` return for exit codes `0`, `1`, and `2`?

0 = No changes are needed. Terraform is already in sync.
1 = An error occurred while creating the plan.
2 = Changes are pending. Terraform has detected differences between the configuration and infrastructure.

### 2. Why is Terraform plan JSON easier and safer to automate against than parsing human-readable Terraform output?

JSON is structured and predictable, making it safer and more reliable for automation than trying to read text intended for humans

### 3. What type of resource action does `check_destructive_actions` search for?

This helps identify resources that Terraform intends to remove.

### 4. Why does finding a `delete` action also help detect replacements?

A replacement involves deleting the existing resource before creating its replacement, so a delete action is evidence that a potentially destructive replacement is planned.

### 5. Why must this script never run `terraform apply`?

The script must never run terraform apply because the purpose of this workflow is to let AI analyze changes safely while keeping real infrastructure changes under human control.

---

# Task 4 — Run the Script Against the Clean Baseline

## Goal

Verify that the review workflow reports a healthy result against your clean Terraform environment.

## Evidence

### Screenshot 7 — Healthy Baseline Report

Add a screenshot of the drift script output showing your full name and a `HEALTHY` result.

![Screenshot 7](<Screenshot 2026-09-21 024139.png>)

---

### Screenshot 8 — Baseline Script Exit Code

Add a screenshot showing the captured script exit code `0`.

![Screenshot 8](<Screenshot 2026-09-21 024712.png>)

## Questions

### 1. What is the Overall Status of your baseline?

The baseline is healthy because Terraform detected no pending changes or errors.

### 2. Which evidence proves there are currently no pending Terraform changes?

The evidence are "Terraform plan exit code: 0" and "No changes. Your infrastructure matches the configuration".

### 3. Was `reports/tfplan.json` created? Explain why or why not.

tfplan.json was not created because the Terraform plan returned exit code 0, meaning there were no changes to inspect.

The script only creates the JSON plan when Terraform returns exit code 2, which means changes are pending.

---

# Task 5 — Create and Run the `/tf-drift-review` Claude Code Skill

## Goal

Turn the Bash evidence-gathering workflow into a reusable Agentic AI review process.

## Evidence

### Screenshot 9 — `/tf-drift-review` Skill Configuration

Add a screenshot of `SKILL.md` showing the frontmatter, allowed tools, and safety rules.

 ![Screenshot 9](<Screenshot 2026-09-21 025850.png>)
![Screenshot 9.1](<Screenshot 2026-09-21 025909.png>)
---

### Screenshot 10 — Clean Agentic AI Review

Add a screenshot of `/tf-drift-review` showing the clean `HEALTHY` result.

![Screenshot 10](<Screenshot 2026-09-21 030318.png>)

## Questions

### 1. Why does this Skill have `Bash`, `Read`, and `Grep`, but not `Write`?

The Skill has tools for gathering and examining evidence, but no tool for changing the project.

### 2. Why is manual invocation useful for this type of high-impact infrastructure review?

It keeps the human in control of when an infrastructure review happens.

### 3. Which part of the workflow is deterministic Bash automation?

The deterministic part is: tf-drift-check.sh

### 4. Which part requires Claude's reasoning?

Claude's reasoning happens after the evidence has been collected. Claude interprets the report and plan JSON to explain:
What changed.
What risks exist.
Whether destructive actions are present.
What the human should review next.

### 5. Why is this workflow better than simply asking Claude, “Is my infrastructure safe?”

The workflow gives Claude specific, verifiable evidence from Terraform and predefined checks.

---

# Task 6 — Introduce a Controlled Difference and Detect It

## Goal

Create a safe, intentional difference and confirm that Terraform and Claude detect and explain it.

## Evidence

### Screenshot 11 — Controlled Difference

Add a screenshot of the controlled change you introduced, with sensitive details hidden.

![Screenshot 11](<Screenshot 2026-09-21 033642.png>)
---

### Screenshot 12 — Detected Difference and Risk Assessment

Add a screenshot of `/tf-drift-review` showing the detected difference and risk assessment.

![Screenshot 12](<Screenshot 2026-09-21 035030.png>)

---

### Screenshot 13 — Detected Drift Report

Add a screenshot of `drift-detected-report.txt` showing your full name and the `WARN` or `FAIL` result.


![Screenshot 13](<Screenshot 2026-09-21 035030-1.png>)

## Questions

### 1. What change did you introduce?

I changed the EC2 security group's HTTP rule in the Terraform configuration from port 80 to port 8080.

### 2. Was it true infrastructure drift or a Terraform configuration change?

It was a Terraform configuration change, not true infrastructure drift.

The AWS infrastructure still had the original port 80 rule. We changed the Terraform code to request port 8080, creating a difference between the configuration and the deployed infrastructure.

### 3. What Terraform plan evidence proves that a change is pending?

Exit code 2 means Terraform detected pending changes.

### 4. Was the action an update, deletion, replacement, or security-rule change?

It was a security-rule change/update to the EC2 security group's HTTP ingress rule.
There were:
0 to add
1 to change
0 to destroy

So it was not a deletion or replacement.

### 5. What did Claude recommend?

Recommendation
The drift review classified the change as WARN because a change is pending.
The change is non-destructive, but it still requires human review because it changes network access to the web server.
### 6. Why should you review the recommendation before taking action?

Write your answer here.
AI reviews the evidence → Human reviews the recommendation → Human decides whether to apply.
---

# Task 7 — Add a `PreToolUse` Hook to Block Unsafe Apply Attempts

## Goal

Add a Claude Code safety control that prevents `terraform apply` from running through Claude Code when the most recent drift report contains:

```text
Overall Status: FAIL
```

## Evidence

### Screenshot 14 — `PreToolUse` Safety Hook

Add a screenshot of `.claude/settings.json` showing the `PreToolUse` safety hook.

![Screenshot 14](<Screenshot 2026-09-21 041327.png>)

---

### Screenshot 15 — Blocked Apply Attempt

Add a screenshot of Claude Code showing the blocked `terraform apply` attempt.

![Screenshot 15](<Screenshot 2026-09-21 042504.png>)

## Questions

### 1. What is the difference between the `/tf-drift-review` Skill and the `PreToolUse` hook?

The /tf-drift-review Skill performs a read-only Terraform review. It gathers the Terraform evidence, analyzes the plan and drift report, identifies risks, and provides a recommendation for human review.

The PreToolUse hook is a safety control that runs before a tool is used. It checks the latest drift report and can block an operation when the report shows FAIL.

### 2. Which component performs analysis?

The /tf-drift-review skill performs the analysis. It uses the Terraform plan, JSON evidence, and drift report to identify changes and assess risks.

### 3. Which component enforces the safety gate?

The PreToolUse hook enforces the safety gate. If the latest report contains Overall Status: FAIL, the hook blocks the operation.

### 4. Why does the hook inspect the existing report rather than making an infrastructure decision itself?

The hook is designed to be a simple, deterministic safety control. It checks the result of the evidence-based review instead of making its own judgment about whether infrastructure is safe to change. This keeps the analysis separate from the safety enforcement mechanism and keeps the human responsible for the final infrastructure decision.

### 5. Why is a deterministic guard useful for high-impact commands?

A deterministic guard provides a predictable rule that is applied consistently every time. For high-impact commands such as terraform apply, this reduces the risk of an unsafe action being executed when a known safety condition has failed.

---

# Task 8 — Resolve the Difference and Verify the Final State

## Goal

Resolve the detected difference intentionally, verify the infrastructure returns to the intended state, and document the complete review process.

## Evidence

### Screenshot 16 — Human-Reviewed Resolution

Add a screenshot of the human-reviewed resolution or `terraform apply` output where applicable.

![Screenshot 16](<Screenshot 2026-09-21 043346-1.png>)

---

### Screenshot 17 — Final Healthy Review

Add a screenshot of the final `/tf-drift-review` showing `HEALTHY`.

![Screenshot 17](<Screenshot 2026-09-21 043346.png>).

---

### Screenshot 18 — Saved Reports

Add a screenshot of `ls -lah reports` showing both:

- `drift-detected-report.txt`
- `resolved-report.txt`

![Screenshot 18](<Screenshot 2026-09-21 045100.png>)

---

### Screenshot 19 — Drift Review Summary

Add a screenshot of `drift-review-summary.md` showing all required sections and your full name.

![Screenshot 19](<Screenshot 2026-09-21 044107.png>)

## Terraform Drift Review Summary

### 1. Change Introduced

Explain the controlled change you introduced.

State whether it was:

- True infrastructure drift, or
- A Terraform configuration change

I changed the EC2 security group's HTTP ingress port in modules/networking/main.tf from 8080 back to 80.

### 2. Evidence Collected

Describe the Terraform plan evidence and affected resource.

Terraform showed 0 to add, 1 to change, and 0 to destroy, confirming that the controlled difference was an in-place security-group change.
The Bash drift-check script also reported no destructive actions and no new open-ingress issue, resulting in an overall WARN status.

### 3. Risk Assessment

Explain the risk identified by the Bash check and Claude Code.

The Bash check identified that a Terraform change was pending but found no destructive actions.

### 4. Human-Approved Action

Explain the action you reviewed and executed manually.

I reviewed the Terraform plan and Claude's analysis. I decided that the intended state was to keep HTTP on port 80, so I reverted the Terraform configuration from port 8080 back to port 80.

A subsequent attempt to run terraform apply through Claude Code was blocked by the configured safety control, demonstrating that the safety gate was working.

### 5. Verification

Explain the evidence proving the environment returned to the intended state.

The final drift check reported:

Terraform plan exit code: 0
PASS checks: 3
WARNING checks: 0
FAIL checks: 0
Overall Status: HEALTHY

The final /tf-drift-review also reported HEALTHY.

### 6. Safety Decision

Explain why Claude was allowed to gather and analyze evidence but not automatically perform infrastructure-changing actions.

Claude was allowed to gather and analyze Terraform evidence because its role in this workflow was to interpret the plan, identify risks, and provide a recommendation.

### 7. Agentic Loop Mapping

Explain how your workflow followed:

```text
Gather --> Analyze --> Human Act --> Verify
```

The workflow followed the Agentic DevOps loop:

Gather → Analyze → Human Act → Verify

Gather: The Bash script ran Terraform plan and collected structured evidence.
Analyze: Claude Code reviewed the drift report and Terraform plan.
Human Act: I reviewed the proposed change and restored the intended HTTP port of 80.
Verify: Terraform plan and the final /tf-drift-review confirmed a HEALTHY state.

## Questions

### 1. What action did you execute to resolve the difference?

I changed the EC2 security group's HTTP ingress port in modules/networking/main.tf from 8080 back to 80.

### 2. Did you review `terraform plan` before taking action?

Yes. Terraform showed 0 to add, 1 to change, and 0 to destroy, confirming that the controlled difference was an in-place security-group change.

### 3. What evidence proves the environment is now aligned?

Terraform reported "No changes. Your infrastructure matches the configuration." The final drift check returned exit code 0 with an overall status of HEALTHY, with 3 PASS checks, 0 warnings, and 0 failures.

### 4. Why is a second drift review required after the fix?

A second drift review confirms that the intended change was actually resolved and that no unexpected changes or new risks remain. It provides evidence that the environment is back in the expected state.

### 5. What could go wrong if an AI agent automatically applied every detected Terraform change?

An AI agent could apply an incorrect, unnecessary, or destructive change without sufficient human review. This could cause service disruption, security problems, data loss, or unexpected AWS costs.

### 6. In one sentence, explain the difference between asking an AI chatbot “Is my infrastructure okay?” and using this evidence-based Agentic AI workflow.

An ordinary chatbot may give an opinion based on limited information, while this Agentic AI workflow gathers actual Terraform evidence, performs defined checks, analyzes the results, keeps high-impact actions under human control, and verifies the final state.

---

# LinkedIn Post — Mandatory

## Goal

Publish a LinkedIn post in your own words describing:

- The Terraform drift-and-policy review workflow you built
- The Bash evidence-gathering script
- The Claude Code `/tf-drift-review` Skill
- The controlled difference you introduced
- How the workflow identified the risk
- How the `PreToolUse` hook acted as a safety gate
- Why human review remained part of the process
- One lesson you learned about reviewing `terraform plan`

Include a screenshot of the detected change and a screenshot of the final `HEALTHY` review in your post.

Suggested tags:

```text
#DMIByPravinMishra #Terraform #AgenticAI #ClaudeCode #DevOps
```

## LinkedIn Evidence

### https://www.linkedin.com/posts/nwoke-onyinye_dmibypravinmishra-agenticai-azuredevops-share-7507655221884510208-YfIZ/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAo3AmwBML7hksPwy4zQreoUkgXVNBf9D1c



### Published LinkedIn Post Screenshot — Mandatory

![LinkedIn Post](<Screenshot 2026-09-22 191055.png>)

---

# Required Assignment Files

Confirm that the following files are included in your GitHub repository:

- `CLAUDE.md`
- `AI Assignment/tf-drift-check.sh`
- `.claude/skills/tf-drift-review/SKILL.md`
- `.claude/settings.json` containing the safety hook
- `reports/drift-detected-report.txt`
- `reports/resolved-report.txt`
- `drift-review-summary.md`

---

# Submission Instructions

- Complete Tasks 1–8 in sequence.
- Include Screenshots 1–19 exactly as specified.
- Answer every question under Tasks 1–8 in your own words.
- Complete all seven sections of the Terraform Drift Review Summary.
- Include the GitHub repository/folder URL containing the assignment files.
- Include your full name in the required reports and screenshots.
- Include the LinkedIn post URL and a screenshot of the published LinkedIn post.
- Do not expose access keys, passwords, tokens, account IDs, private keys, Terraform secrets, or other sensitive information.
- Review all screenshots carefully and hide or redact sensitive details where necessary.

---

# Completion Checklist

- [ ] Confirmed a clean Terraform baseline
- [ ] Created the required assignment workspace
- [ ] Created or updated `CLAUDE.md`
- [ ] Added project context and safety rules
- [ ] Created `tf-drift-check.sh`
- [ ] Added my full name to the report
- [ ] Validated the Bash script
- [ ] Made the script executable
- [ ] Used `terraform plan -detailed-exitcode`
- [ ] Used Terraform plan JSON
- [ ] Used `jq` to inspect destructive actions
- [ ] Used `jq` to inspect unsafe ingress
- [ ] Confirmed the baseline returns `HEALTHY`
- [ ] Created `/tf-drift-review`
- [ ] Restricted the Skill to appropriate tools
- [ ] Confirmed the Skill remains read-only
- [ ] Confirmed the Skill never runs `terraform apply`
- [ ] Confirmed the Skill never runs `terraform destroy`
- [ ] Introduced a controlled detectable difference
- [ ] Correctly identified whether it was true drift or a configuration change
- [ ] Saved `drift-detected-report.txt`
- [ ] Added the `PreToolUse` safety hook
- [ ] Verified the hook blocks `terraform apply` when the report is `FAIL`
- [ ] Reviewed the Terraform evidence before resolving the change
- [ ] Performed any infrastructure-changing action manually
- [ ] Ran the drift review again after resolution
- [ ] Confirmed the final status is `HEALTHY`
- [ ] Saved `resolved-report.txt`
- [ ] Completed `drift-review-summary.md`
- [ ] Mapped the workflow to `Gather --> Analyze --> Human Act --> Verify`
- [ ] Included all 19 numbered screenshots
- [ ] Answered all required questions
- [ ] Published the required LinkedIn post
- [ ] Added the LinkedIn post URL and screenshot
- [ ] Included the GitHub repository/folder URL
- [ ] Confirmed that no sensitive information is exposed

---

*This submission is part of the DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
