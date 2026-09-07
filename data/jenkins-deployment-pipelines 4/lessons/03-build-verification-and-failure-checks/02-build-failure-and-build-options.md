# Jenkins Build Failure and Jenkins Build Options

## Overview

This document explains how to understand and troubleshoot a failed Jenkins build and what the different options available on a Jenkins build page are used for.

This is intended for **new joiners who are not yet familiar with Jenkins**.

The most important rule when troubleshooting a failed Jenkins build is:

> **Start with the Console Output.**

The Console Output contains the logs generated during the build and is usually the first place to identify why the build failed.

---

# 1. What Can Cause a Jenkins Build to Fail?

A Jenkins build can fail for different reasons.

Common reasons include:

### Bitbucket / Git Fetch Failure

Jenkins may fail while fetching the source code from Bitbucket.

This means Jenkins could not successfully obtain the branch, commit, or source code required for the build.

### Vulnerability Failure

A security or vulnerability check may fail.

In this case, the build may stop because the configured security check did not pass.

### Test Case Failure

One or more automated test cases may fail during the build.

The build can therefore fail even if the source code was successfully fetched and compiled.

### Other Check Failure

A Jenkins pipeline can contain other validation or quality checks.

If one of these checks fails, the Jenkins build can also fail.

---

# 2. What Should I Do When a Jenkins Build Fails?

If a build fails, follow this simple process:

**Failed Build**

↓

**Open the Build Number**

↓

**Open Console Output**

↓

**Find the error**

↓

**Identify the failed stage**

↓

**Understand the reason for failure**

The Console Output should normally be checked before looking at other Jenkins options.

---

# 3. Where Can I Find the Reason for a Failed Build?

Open the failed **Build Number** and select:

**Console Output**

Console Output contains the logs generated while Jenkins executes the build.

Look for messages that indicate:

* Error
* Failure
* Exception
* Failed stage
* Test failure
* Git/Bitbucket failure
* Vulnerability/security failure

The important point is that the build status tells you **that** the build failed, while Console Output helps explain **why** it failed.

---

# 4. Jenkins Build Number Page

When you open a specific Jenkins build number, Jenkins provides several options on the left side.

Each option provides different information about that particular build.

The important options include:

* Status
* Changes
* Console Output
* View Build Information
* Parameters
* Timings
* Git Build Data
* See Fingerprints
* Lockable Resources
* Rebuild
* Open Blue Ocean
* Restart from Stage
* Pipeline Steps
* Previous Build
* Next Build

---

# 5. What is Status in Jenkins?

**Status** shows the current or final result of a Jenkins build.

Common statuses include:

* **Running** — the build is currently executing.
* **Success** — the build completed successfully.
* **Failed** — the build did not complete successfully.
* **Aborted** — the build was stopped before completion.

### New Joiner Understanding

If someone asks:

**"How do I know whether my Jenkins build succeeded?"**

Check the **Status** of the build.

If it shows **Success**, the build completed successfully.

If it shows **Failed**, the build encountered a failure.

If it shows **Running**, the build has not completed yet.

---

# 6. What is Changes in Jenkins?

**Changes** shows the code changes or Git commits included in the build.

It helps answer:

> "What code changes were included in this build?"

### When should I use Changes?

Use **Changes** when you want to:

* See commits included in the build.
* Understand what changed between builds.
* Identify which code changes may be related to a failure.

### Example

If Build #100 succeeds and Build #101 fails, checking **Changes** can help identify what code changes were introduced in Build #101.

---

# 7. What is Console Output in Jenkins?

**Console Output** shows the logs generated during the Jenkins build.

These logs show what Jenkins did while executing the pipeline.

Console Output is usually the **first place to check when a build fails**.

### What can I find in Console Output?

You can use it to identify:

* The stage where the build failed.
* The command that failed.
* The error message.
* Git/Bitbucket problems.
* Test failures.
* Vulnerability failures.
* Other pipeline errors.

### New Joiner Rule

If someone asks:

**"My Jenkins build failed. Where should I look first?"**

Answer:

> Open the failed build and check **Console Output** first. It contains the build logs and usually provides the reason for the failure.

---

# 8. What is View Build Information?

**View Build Information** provides general information about a Jenkins build.

It can show information such as:

* Build number
* Build duration
* Start time
* Build result
* Who or what triggered the build

### When should I use it?

Use this option when you need basic information about the build rather than detailed failure logs.

For example:

**"When did this build start?"**

**"How long did this build take?"**

**"What triggered this build?"**

View Build Information can help answer these questions.

---

# 9. What are Parameters in Jenkins?

**Parameters** shows the parameters and values used when the Jenkins build was triggered.

Some Jenkins jobs allow users to provide inputs before starting a build.

For example, a job may allow users to select:

* A branch
* An environment
* A deployment option
* Another configurable value

The exact parameters depend on how the Jenkins job is configured.

### When should I check Parameters?

Check **Parameters** when you want to confirm:

> "What inputs were provided when this build was started?"

### Example

If the build was expected to run against one branch but appears to have used another branch, the build parameters may help identify what was selected.

---

# 10. What is Timings in Jenkins?

**Timings** shows how much time different parts of the build took.

It is useful for understanding build duration.

### When should I use Timings?

Use **Timings** when:

* A build is taking unusually long.
* You want to identify a slow stage.
* You want to compare execution time between builds.

### Example

Suppose a pipeline has:

**Build → Test → Security Scan → Package**

If the Security Scan takes most of the build time, Timings can help identify that.

---

# 11. What is Git Build Data?

**Git Build Data** provides information about the Git source used for the build.

It can show information such as:

* Branch
* Commit
* Revision

### Why is Git Build Data useful?

It helps answer:

> "Which branch and commit did Jenkins actually build?"

This is important when verifying whether the expected code was built.

### Example

If a developer says:

> "I pushed commit `abc123` to master."

You can use the Git-related build information to verify which commit Jenkins used.

---

# 12. What is See Fingerprints?

**See Fingerprints** shows artifact fingerprints associated with the Jenkins build.

Jenkins can use fingerprints to track artifacts across different builds and jobs.

### When should a new joiner use this?

This is mainly useful for advanced artifact tracking.

A new joiner generally does not need to use this option for normal build-failure troubleshooting.

Use it when you need to trace an artifact and understand which Jenkins build produced or used it.

---

# 13. What are Lockable Resources?

**Lockable Resources** shows resources that were locked or reserved during the build.

A resource can be locked to prevent multiple builds from using the same resource at the same time.

### Why is this needed?

Imagine two Jenkins builds both try to use the same server or environment.

If both builds use it simultaneously, they may interfere with each other.

A lock can prevent this by allowing only one build to use the resource at a time.

### When should I check Lockable Resources?

Check this when a build appears to be waiting for or using a shared resource.

---

# 14. What is Rebuild in Jenkins?

**Rebuild** allows a Jenkins build to be run again.

Depending on the Jenkins job configuration, the previous build's parameters/configuration can be reused or modified.

### When should I use Rebuild?

Use **Rebuild** when you want to retry a build.

For example:

A build fails because of a temporary issue.

You can use **Rebuild** to run the build again.

### Important

A rebuild should not be treated as a fix for every failure.

First understand **why the original build failed**.

If the failure is caused by a code issue, simply rebuilding will usually not solve the problem.

---

# 15. What is Open Blue Ocean?

**Open Blue Ocean** opens the Jenkins build in the Blue Ocean interface.

Blue Ocean provides a more visual representation of Jenkins Pipeline execution.

It can make it easier to understand:

* Pipeline stages
* Stage status
* Successful stages
* Failed stages
* Overall pipeline flow

### When should I use it?

Use Blue Ocean when you want a more visual view of the Pipeline.

For basic troubleshooting, **Console Output remains the primary place to check the actual error logs**.

---

# 16. What is Restart from Stage?

**Restart from Stage** allows a Jenkins Pipeline to be restarted from a particular stage instead of running the entire Pipeline from the beginning.

### Example

Suppose the Pipeline contains:

**Build → Test → Security Check → Deploy**

If the Pipeline reaches Deploy but Deploy fails, Jenkins may allow the Pipeline to be restarted from the Deploy stage.

This avoids repeating earlier stages that have already completed successfully.

### Important

The option is available only when the Pipeline is configured to support restarting from stages.

It should be used only when it is appropriate to skip the earlier stages.

---

# 17. What is Pipeline Steps?

**Pipeline Steps** shows the detailed internal steps executed by the Jenkins Pipeline.

This provides a deeper view of what the Pipeline is doing internally.

### When should I use it?

Pipeline Steps is mainly useful for advanced troubleshooting.

A new joiner should normally start with:

**Console Output**

and move to Pipeline Steps only when additional technical information is required.

---

# 18. What is Previous Build?

**Previous Build** opens the build immediately before the current build.

### Why is it useful?

It allows you to compare the current build with the previous build.

For example:

**Build #100 → Success**

**Build #101 → Failed**

If Build #100 succeeded and Build #101 failed, you can investigate what changed between the two builds.

Useful information to compare includes:

* Changes
* Git commit
* Build parameters
* Console Output
* Build duration

---

# 19. What is Next Build?

**Next Build** opens the build immediately after the current build, if one exists.

### When should I use it?

It can be useful when investigating a sequence of builds.

For example:

**Build #100 → Failed**

**Build #101 → Success**

Looking at Build #101 can help determine whether the issue was temporary or whether something changed in the following build.

---

# 20. Which Jenkins Option Should I Use?

Use this simple guide:

| If you want to know...               | Check                      |
| ------------------------------------ | -------------------------- |
| Did the build succeed?               | **Status**                 |
| Why did the build fail?              | **Console Output**         |
| What code changed?                   | **Changes**                |
| Which branch/commit was built?       | **Git Build Data**         |
| Which inputs were provided?          | **Parameters**             |
| Which stage was slow?                | **Timings**                |
| What happened in the previous build? | **Previous Build**         |
| Retry the build                      | **Rebuild**                |
| View the Pipeline visually           | **Open Blue Ocean**        |
| Restart from a particular stage      | **Restart from Stage**     |
| See detailed Pipeline execution      | **Pipeline Steps**         |
| Investigate shared resources         | **Lockable Resources**     |
| Track build artifacts                | **See Fingerprints**       |
| Inspect general build information    | **View Build Information** |
| Inspect a following build            | **Next Build**             |

---

# 21. Simple Jenkins Troubleshooting Flow for New Joiners

When a new joiner asks:

> **"My Jenkins build failed. What should I do?"**

The recommended answer is:

### Step 1 — Open the failed build

Open the Jenkins job and select the failed **Build Number**.

### Step 2 — Open Console Output

Go to:

**Build Number → Console Output**

### Step 3 — Find the failure

Look for the error or failure message.

Identify which Pipeline stage failed.

### Step 4 — Understand the failure type

Determine whether the failure is related to:

* Bitbucket/Git
* Test cases
* Vulnerability/security checks
* Another pipeline check

### Step 5 — Use additional Jenkins options if needed

Depending on the problem:

* Check **Git Build Data** for branch/commit information.
* Check **Changes** for included commits.
* Check **Parameters** for build inputs.
* Check **Timings** for slow stages.
* Check **Previous Build** to compare with the last build.
* Use **Rebuild** to retry when appropriate.
* Use **Pipeline Steps** for advanced investigation.

---

# 22. Common New Joiner Questions

## "Where should I check first if my Jenkins build fails?"

Check **Console Output** first.

It contains the logs generated during the build and usually provides the reason for the failure.

---

## "How do I know which commit Jenkins built?"

Check **Git Build Data**.

It provides the branch, commit, and revision used by the build.

---

## "How do I know what code changes were included?"

Check **Changes**.

It shows the Git commits/code changes included in the build.

---

## "My build failed. Should I immediately click Rebuild?"

No.

First check **Console Output** and understand why the build failed.

If the failure was temporary and the build can safely be retried, **Rebuild** can be used.

---

## "What is the difference between Status and Console Output?"

**Status** tells you the result of the build.

Example:

**Failed**

**Console Output** helps explain why it failed.

---

## "What is the difference between Changes and Git Build Data?"

**Changes** shows the code changes/commits included in the build.

**Git Build Data** provides Git information such as the branch, commit, and revision used by Jenkins.

---

## "When should I use Pipeline Steps?"

Use **Pipeline Steps** when you need deeper information about the internal steps executed by the Jenkins Pipeline.

For normal build troubleshooting, start with **Console Output**.

---

## "When should I use Previous Build?"

Use **Previous Build** when you want to compare the current build with the build immediately before it.

This is especially useful when a previously successful build is followed by a failed build.

---

## "What if my build is taking too long?"

Check **Timings** to identify which part or stage of the build is taking the most time.

---

# 23. Important Beginner Mental Model

A Jenkins build page contains different types of information.

Think of the options like this:

**Status**

→ What happened?

**Console Output**

→ Why did it happen?

**Changes**

→ What code changed?

**Git Build Data**

→ What code/commit was actually built?

**Parameters**

→ What inputs were used?

**Timings**

→ How long did it take?

**Previous Build**

→ What happened last time?

**Rebuild**

→ Can I run it again?

**Restart from Stage**

→ Can I continue from a later stage?

**Pipeline Steps**

→ What did Jenkins execute internally?

This mental model should be used when explaining Jenkins build options to a new joiner.

---

# 24. Important Rules for Answering Jenkins Questions

When answering questions related to Jenkins build failures and build options:

1. **Explain the concept in simple language first.**
2. Avoid assuming that the user already knows Jenkins terminology.
3. Explain technical terms when they first appear.
4. For troubleshooting questions, provide a clear step-by-step action.
5. Always distinguish between:

    * What the option shows.
    * Why the information is useful.
    * When the user should use it.
6. Use simple examples wherever possible.
7. For build failures, recommend **Console Output first**.
8. Do not assume that a failed build is caused by a code change. The failure may be caused by Git/Bitbucket, tests, security checks, infrastructure, or another pipeline check.
9. Do not recommend **Rebuild** as the first troubleshooting step. First understand the failure.
10. Do not invent Jenkins options, parameters, URLs, pipeline names, commands, or organization-specific behavior that is not documented in the available context.

---

# 25. One-Line Summary

For a new joiner:

> **When a Jenkins build fails, open the failed build, check Console Output first to understand why it failed, and then use Changes, Git Build Data, Parameters, Timings, Previous Build, or other options depending on what you need to investigate.**
