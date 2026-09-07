# Build Master and Other Branches

## Overview

Jenkins is used to build the code of a service.

A service can have multiple Git branches, such as:

* `master`
* Feature branches
* Development branches
* Other project-specific branches

Jenkins can build code from these branches depending on how the pipeline is configured.

The important idea for a new joiner is:

**Different branches can be built separately, and the Jenkins build pipeline is responsible for building the code from the selected branch.**

---

## Build Pipeline

There will be a Jenkins build pipeline for each service.

This applies to both:

* UI services
* Backend services

For example, a backend service may have a Jenkins pipeline named:

`home-loan-home-loan-orchestrator-build`

The exact pipeline name depends on the service name.

---

## Understanding the Pipeline Name

A Jenkins build pipeline generally contains the service name and indicates that the pipeline is used to build that service.

Common naming patterns include:

### Backend service

```text
service-name-orchestrator-build
```

Example:

```text
home-loan-home-loan-orchestrator-build
```

Here:

* `home-loan-home-loan-orchestrator` → service name
* `build` → Jenkins pipeline is used to build the service

### UI service

```text
servicename-ui-build
```

For example:

```text
home-loan-ui-build
```

Here:

* `home-loan` → service name
* `ui` → indicates that it is a UI service
* `build` → Jenkins pipeline is used to build the service

### General service

```text
servicename-build
```

The exact naming convention can vary between services.

---

## Master Branch Build

The `master` branch represents one of the important branches used in the deployment flow.

When code is merged into `master`, the corresponding Jenkins build pipeline can build the latest code from `master`.

The basic flow is:

```text
Developer changes code
        ↓
Code is committed
        ↓
Code is merged into master
        ↓
Jenkins builds the service
        ↓
Build checks are performed
        ↓
Build succeeds or fails
```

For example:

```text
home-loan-home-loan-orchestrator
                ↓
             master
                ↓
home-loan-home-loan-orchestrator-build
```

The Jenkins build uses the code corresponding to the branch/commit being built.

---

## Other Branch Builds

Jenkins can also be used to build code from branches other than `master`, depending on the pipeline configuration.

For example:

```text
feature/ABC-123
        ↓
Jenkins Build
```

The purpose of building another branch can be to verify the changes in that branch before they are merged.

A branch build helps answer:

> "Does the code in this branch build successfully?"

It does **not** by itself mean that the code has been merged into `master` or deployed to an environment.

---

## Master Build vs Other Branch Build

The main difference is the source of the code being built.

| Build              | Code being built              | Purpose                            |
| ------------------ | ----------------------------- | ---------------------------------- |
| Master build       | Code from `master`            | Build the current master code      |
| Other branch build | Code from the selected branch | Verify/test changes in that branch |

For a new joiner, remember:

**Jenkins build number and Git branch are different things.**

For example:

```text
Jenkins Build #3340
```

is a Jenkins build number.

Whereas:

```text
master
```

or

```text
feature/ABC-123
```

is a Git branch.

The build number identifies a particular Jenkins execution, while the branch identifies the source code being built.

---

## How to Understand a Build

When looking at a Jenkins build, a new joiner should first identify:

1. **Which service is being built?**
2. **Which branch is being built?**
3. **Which commit is being built?**
4. **What is the Jenkins build number?**
5. **Did the build succeed or fail?**

For example:

```text
Service: home-loan-ui
Branch: master
Build: #3340
Commit: ad9cb...
Status: SUCCESS
```

This means Jenkins successfully built the `home-loan-ui` service using the specified code.

---

## Important Relationship

A useful mental model is:

```text
Git Branch
    ↓
Git Commit
    ↓
Jenkins Build
    ↓
Build Result
```

For example:

```text
master
  ↓
latest commit: ad9cb...
  ↓
Jenkins build #3340
  ↓
SUCCESS
```

This relationship becomes important when verifying whether Jenkins built the expected code.

---

## Common New Joiner Questions

### What is a Jenkins build pipeline?

A Jenkins build pipeline is a Jenkins job used to build a particular service.

### Does every service have a build pipeline?

There is a build pipeline for each service, including UI and backend services.

### How do I identify the pipeline for a service?

Look for a Jenkins pipeline containing the service name and a build-related suffix such as:

```text
service-name-build
service-name-ui-build
service-name-orchestrator-build
```

### Is the Jenkins build number the same as the Git commit?

No.

The Jenkins build number identifies the Jenkins execution.

The Git commit identifies the exact code revision being built.

### Can a branch other than `master` be built?

Yes, depending on the Jenkins pipeline configuration.

### If my branch build succeeds, does that mean it is deployed?

No.

A successful branch build means the code was successfully built and passed the checks included in that build. It does not by itself mean that the branch has been merged or deployed.

### Why do we build a branch before merging?

A branch build can help verify that the changes in the branch can be built successfully before the changes are merged into `master`.

---

## Quick Reference

```text
Service
   ↓
Git branch
   ↓
Git commit
   ↓
Jenkins build pipeline
   ↓
Jenkins build number
   ↓
Build result
```

Example:

```text
home-loan-ui
     ↓
master
     ↓
commit: ad9cb...
     ↓
home-loan-ui-build
     ↓
Build #3340
     ↓
SUCCESS
```

## Rules for Answering Jenkins Build Questions

When answering a question about Jenkins builds:

* Explain the concept in simple terms first.
* Clearly distinguish between a **service**, **Git branch**, **Git commit**, **Jenkins pipeline**, and **Jenkins build number**.
* Do not assume that a new joiner already understands Jenkins terminology.
* When discussing a build, identify which branch and commit are being built.
* Do not treat a successful build as proof that the code was deployed.
* Use the service-specific pipeline naming examples when explaining how to identify a build pipeline.
* If the question is about verifying the exact code built by Jenkins, connect the explanation to the **Git commit → Jenkins build → image** relationship.
* If the provided context does not specify a branch or pipeline configuration, do not assume one; explain that the exact behavior depends on the pipeline configuration.
