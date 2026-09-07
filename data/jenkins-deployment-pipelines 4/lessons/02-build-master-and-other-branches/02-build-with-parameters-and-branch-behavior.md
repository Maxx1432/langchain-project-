# Build With Parameters and Branch Behavior

## Overview

A Jenkins build pipeline can be started using **Build With Parameters**.

When starting the build, the user provides a **branch parameter**. The selected branch determines what Jenkins does after the build.

The important idea for a new joiner is:

> **The branch selected in Build With Parameters controls which code Jenkins builds and what happens after the build.**

The supported branch values are:

* `Feature`
* `Hotfix`
* `Custom`
* `Release`
* `Master`

---

# Build With Parameters

## What is Build With Parameters?

**Build With Parameters** is a Jenkins option used to start a build while providing required input values.

For this build pipeline, the important input is the **branch parameter**.

A new joiner can think of it as:

```text
Build With Parameters
        ↓
Select/provide branch
        ↓
Jenkins builds the selected branch
        ↓
Branch-specific behavior happens
```

For example, if the branch parameter is set to `Master`, Jenkins builds the `master` branch.

If another branch type is selected, Jenkins performs the corresponding branch build behavior.

---

# Branch Parameter Values

The build pipeline supports the following branch values:

| Branch Parameter | Meaning                         |
| ---------------- | ------------------------------- |
| `Feature`        | Build a feature branch          |
| `Hotfix`         | Build a hotfix branch           |
| `Custom`         | Build a custom-specified branch |
| `Release`        | Build a release branch          |
| `Master`         | Build the `master` branch       |

The exact branch name or additional input required for values such as `Feature`, `Hotfix`, `Custom`, or `Release` depends on how the Jenkins pipeline is configured.

---

# Master Branch Behavior

When `Master` is passed as the branch parameter, Jenkins builds the `master` branch.

If the build is successful, the branch is also deployed to:

1. **SIT**
2. **QA**

The flow is:

```text
Build With Parameters
        ↓
Branch = Master
        ↓
Build master
        ↓
Build successful
        ↓
Deploy to SIT
        ↓
Deploy to QA
```

### Example

Suppose the service is:

```text
home-loan-ui
```

and the branch parameter is:

```text
Master
```

Jenkins will:

```text
Build master
      ↓
Build successful
      ↓
SIT deployment
      ↓
QA deployment
```

For the documented flow, there should be no deployment issue when the successful `master` build proceeds through SIT and QA.

---

# Other Branch Behavior

When a branch other than `Master` is passed, the behavior is different.

For other branches, Jenkins:

1. Builds the selected branch.
2. Creates an image for that branch.
3. The generated image can then be used by other deployment processes.

The flow is:

```text
Build With Parameters
        ↓
Select another branch
        ↓
Build branch
        ↓
Create Image
        ↓
Image can be used in other deployment processes
```

For example:

```text
Feature branch
      ↓
Jenkins build
      ↓
Image created
      ↓
Image available for deployment
```

A successful build of another branch does **not** mean that the branch is automatically deployed to SIT or QA through this flow.

The important distinction is:

> **Master build → successful build can proceed to SIT and QA deployment.**

> **Other branch build → image is created and can be used by other deployment processes.**

---

# Master vs Other Branches

This distinction is important for new joiners.

| Selected Branch | Jenkins Behavior                                         |
| --------------- | -------------------------------------------------------- |
| `Master`        | Builds `master`; successful build proceeds to SIT and QA |
| `Feature`       | Builds the feature branch and creates an image           |
| `Hotfix`        | Builds the hotfix branch and creates an image            |
| `Custom`        | Builds the specified custom branch and creates an image  |
| `Release`       | Builds the release branch and creates an image           |

For non-master branches, the created image can be used by other deployment processes.

---

# What is an Image?

In this context, an **image** is the packaged output created from the service build that can be used during deployment.

A simple mental model is:

```text
Git Branch
    ↓
Code
    ↓
Jenkins Build
    ↓
Image
    ↓
Deployment
```

For a `master` build, the image is used as part of the automated deployment flow to SIT and QA.

For other branches, the image is created and can be used by other deployment processes.

---

# Automated SCM

## What is Automated SCM?

The build/deployment flow may use an automated SCM trigger to start the next deployment-related process.

Sometimes the automated SCM trigger is **not triggered**.

When this happens, the deployment can be triggered manually.

The important point is:

> **If the automated SCM trigger does not start the deployment, the deployment does not necessarily need to wait for the automatic trigger. It can be manually triggered for the required environment.**

---

# Manual Deployment

If automated SCM is not triggered, deployment can be manually started for:

* **SIT**
* **QA**

The basic flow becomes:

```text
Build successful
      ↓
Automated SCM trigger
      ↓
Trigger received?
   ↙          ↘
 Yes           No
 ↓              ↓
Deployment    Manual trigger
                  ↓
              SIT / QA
```

For example:

```text
Master build successful
        ↓
Automated SCM not triggered
        ↓
Manually trigger deployment
        ↓
SIT
```

or:

```text
Master build successful
        ↓
Automated SCM not triggered
        ↓
Manually trigger deployment
        ↓
QA
```

---

# Important Difference: Build vs Deployment

A common confusion for new joiners is treating a successful build and deployment as the same thing.

They are different steps.

### Build

The build verifies/builds the code and produces the required output/image.

```text
Code → Jenkins → Build
```

### Deployment

Deployment takes the generated build/image and makes it available in an environment such as SIT or QA.

```text
Image → Deployment → SIT/QA
```

Therefore:

> **A successful build does not always mean that the application is already deployed.**

For the `master` flow, a successful build proceeds to SIT and QA deployment.

For other branches, the build creates an image that can be used by other deployment processes.

---

# Common New Joiner Questions

### What should I use to start the build?

Use **Build With Parameters**.

### What parameter do I need to provide?

Provide the **branch parameter**.

### What branch values are available?

The documented values are:

`Feature`, `Hotfix`, `Custom`, `Release`, and `Master`.

### What happens if I select Master?

Jenkins builds the `master` branch.

If the build is successful, it proceeds to deployment in:

```text
SIT → QA
```

### What happens if I select a feature branch?

Jenkins builds the feature branch and creates an image.

The image can then be used in other deployment processes.

### Does a feature branch automatically deploy to SIT and QA?

Not through the documented master-branch flow.

For other branches, the documented behavior is to create an image that can be used by other deployment processes.

### What happens if automated SCM is not triggered?

The deployment can be manually triggered for the required environment, including SIT or QA.

### Is Build With Parameters the same as deployment?

No.

**Build With Parameters** starts the Jenkins build and provides the required inputs. Deployment is a subsequent process that uses the build/image.

### Why does the selected branch matter?

Because the branch determines which code Jenkins builds and, importantly, whether the build follows the master deployment flow or the other-branch image creation flow.

---

# Quick Reference

```text
BUILD WITH PARAMETERS
        ↓
Provide Branch Parameter
        ↓
 ┌───────────────┐
 │ Which branch? │
 └───────┬───────┘
         │
    ┌────┴─────┐
    │          │
  Master    Other Branch
    │          │
    ↓          ↓
  Build      Build
    │          │
    ↓          ↓
 Success     Create Image
    │          │
    ↓          ↓
   SIT       Used by other
    ↓        deployment
   QA        processes
```

If the automated SCM trigger does not start the deployment:

```text
Manual deployment trigger
        ↓
     SIT / QA
```

---

# Beginner Mental Model

Remember these three rules:

### Rule 1 — Build With Parameters selects the branch

```text
Build With Parameters → Branch
```

### Rule 2 — Master has an automatic deployment flow

```text
Master
  ↓
Build
  ↓
Success
  ↓
SIT
  ↓
QA
```

### Rule 3 — Other branches primarily produce an image

```text
Other Branch
    ↓
Build
    ↓
Image
    ↓
Used by other deployment processes
```

---

# Rules for Answering Questions About Build Parameters

When answering a new joiner's question:

* Explain **Build With Parameters** before discussing the branch values.
* Clearly explain that the **branch parameter determines which code is built**.
* Always distinguish `Master` behavior from other branch behavior.
* Explain `Master` as **build → successful build → SIT → QA**.
* Explain other branches as **build → image creation → image available for other deployment processes**.
* Do not say that every branch is automatically deployed to SIT or QA.
* Explain that a build and deployment are separate concepts.
* If automated SCM is not triggered, explain that deployment can be manually triggered for SIT or QA.
* Do not assume additional branch-specific behavior for `Feature`, `Hotfix`, `Custom`, or `Release` beyond what is documented here.
* If the user asks how a particular branch value maps to an exact Git branch name, explain that the exact mapping depends on the pipeline configuration unless that mapping is explicitly documented.
