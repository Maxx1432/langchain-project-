# Master Branch Build Flow

## Overview

The **master branch build flow** describes what happens after code is merged into `master` and the changes need to move through the deployment pipeline.

The overall flow is:

```text id="8z1q4p"
Code merged into master
        ↓
Jenkins Build
        ↓
Build / Quality / Security Checks
        ↓
Build Successful
        ↓
Generate Version Manifest
        ↓
SIT Deployment
        ↓
SIT Deployment Successful
        ↓
SIT Smoke Tests
        ↓
Smoke Tests Successful
        ↓
Promote to QA
        ↓
QA Deployment
```

For a new joiner, the most important thing to understand is:

> **A successful master build does not directly mean the application is ready for QA. The application moves through multiple validation steps: build → SIT deployment → smoke tests → QA.**

---

# Step 1 — Master Branch Build

Example Jenkins pipeline:

```text id="3f8n2k"
home-loan-home-loan-ui-build #3340
```

When code is merged into the `master` branch, the Jenkins build is triggered.

Jenkins builds the **Home Loan UI** and runs the checks configured for the build.

These checks include:

* Build checks
* Quality checks
* Security checks

The basic flow is:

```text id="0x8q3d"
Code merged into master
        ↓
Jenkins build triggered
        ↓
Build checks
        ↓
Quality checks
        ↓
Security checks
```

### What does the build do?

The build takes the code from the relevant commit and creates the build output required for the next stages.

It also verifies that the code passes the checks configured in the pipeline.

---

# Step 2 — Build Successful

Example result:

```text id="v5s2lm"
BUILD SUCCESSFUL
```

If the master branch build completes successfully, the build is automatically promoted to the next stage: **SIT**.

This means the build has passed the checks required by the Jenkins build pipeline.

The flow continues:

```text id="q1r7zw"
Master Build
     ↓
BUILD SUCCESSFUL
     ↓
Proceed towards SIT
```

### Important

A successful build means the **build stage succeeded**.

It does not yet mean that the application has been successfully deployed to SIT.

SIT deployment is a separate step.

---

# Step 3 — Generate Version Manifest

Example Jenkins pipeline:

```text id="k7m4pd"
home-loan-generate-version-manifest #3410
```

After the successful master build, the version manifest is generated.

The **version manifest** contains the version and deployment metadata required for the application release.

The flow is:

```text id="l0x6sr"
Build Successful
       ↓
Generate Version Manifest
       ↓
Version / Deployment Metadata
```

For a new joiner, the simple understanding is:

> **The version manifest provides the version and deployment information needed by the subsequent deployment process.**

---

# Step 4 — SIT Deployment

Example Jenkins pipeline:

```text id="a6j9kt"
home-loan-sit-deploy #2673
```

The successfully built artifact/version is deployed to the **SIT environment**.

SIT is the environment where the application can be validated after deployment.

The flow is:

```text id="1z8q0c"
Successful Build
      ↓
Version Manifest
      ↓
SIT Deploy
      ↓
Application deployed to SIT
```

The SIT deployment can also be triggered directly by manually running the **SIT Deploy** pipeline.

This is useful when the deployment needs to be started manually rather than relying on the automated flow.

---

# Step 5 — SIT Deployment Successful

Example result:

```text id="e2y6hs"
SIT DEPLOYMENT SUCCESSFUL
```

Once the application has been successfully deployed to SIT, the next validation step is triggered.

Automated **SIT smoke tests** are triggered after successful deployment.

The flow becomes:

```text id="q8n4cw"
SIT Deployment
      ↓
SIT DEPLOYMENT SUCCESSFUL
      ↓
SIT Smoke Test
```

---

# Step 6 — SIT Smoke Test

Example Jenkins pipeline:

```text id="7d3k1m"
home-loan-sit-run-smoke #3235
```

The SIT smoke test performs basic validation of the application running in SIT.

The purpose is to check that the deployed application is working correctly at a basic level.

The flow is:

```text id="s4x9nb"
Application deployed to SIT
        ↓
Smoke Test
        ↓
Basic application validation
```

### Why is the smoke test important?

A build can succeed and the deployment can also succeed, but the application may still have a problem after deployment.

The smoke test provides an additional validation step before the build is promoted to QA.

---

# Step 7 — Smoke Tests Successful

Example result:

```text id="2j5q8v"
SMOKE TESTS GREEN / SUCCESSFUL
```

The smoke tests must pass successfully before the build is promoted to QA.

The flow is:

```text id="n7c3x1"
SIT Smoke Test
      ↓
SMOKE TESTS GREEN
      ↓
Build promoted to QA
```

If the smoke tests are not successful, the build should not be considered successfully promoted to QA.

The important rule is:

> **Smoke tests must pass before the build moves to QA.**

---

# Step 8 — QA Deployment

Example Jenkins pipeline:

```text id="m3v8kq"
home-loan-qa-deploy #2225
```

Once the SIT smoke tests are successful, the build is promoted to the **QA environment**.

The documented flow is:

```text id="r9w2hf"
Smoke Tests Successful
        ↓
BUILD PROMOTED TO QA
        ↓
QA Deployment
```

The QA deployment pipeline is:

```text
home-loan-qa-deploy #2225
```

---

# Complete Master Branch Flow

For a new joiner, remember the entire process as:

```text id="c6p1za"
1. Code merged into master
            ↓
2. home-loan-home-loan-ui-build #3340
            ↓
3. Build / Quality / Security Checks
            ↓
4. BUILD SUCCESSFUL
            ↓
5. home-loan-generate-version-manifest #3410
            ↓
6. home-loan-sit-deploy #2673
            ↓
7. SIT DEPLOYMENT SUCCESSFUL
            ↓
8. home-loan-sit-run-smoke #3235
            ↓
9. SMOKE TESTS GREEN / SUCCESSFUL
            ↓
10. BUILD PROMOTED TO QA
            ↓
11. home-loan-qa-deploy #2225
```

---

# Simple Mental Model

A new joiner can remember the process using four major stages:

```text id="p8v4jx"
BUILD
  ↓
DEPLOY TO SIT
  ↓
TEST IN SIT
  ↓
DEPLOY TO QA
```

More specifically:

```text id="q5t2nm"
Code
 ↓
Build
 ↓
Manifest
 ↓
SIT
 ↓
Smoke Test
 ↓
QA
```

Each stage answers a different question:

| Stage            | Main Question                                                  |
| ---------------- | -------------------------------------------------------------- |
| Build            | Can the code be successfully built and pass configured checks? |
| Version Manifest | What version/deployment metadata should be used?               |
| SIT Deployment   | Can the built application be deployed to SIT?                  |
| Smoke Test       | Is the deployed application basically working in SIT?          |
| QA Deployment    | Can the successfully validated build move to QA?               |

---

# Important Jenkins Pipelines in This Flow

| Purpose                   | Jenkins Pipeline                      | Example Build |
| ------------------------- | ------------------------------------- | ------------- |
| Build                     | `home-loan-home-loan-ui-build`        | `#3340`       |
| Generate version manifest | `home-loan-generate-version-manifest` | `#3410`       |
| SIT deployment            | `home-loan-sit-deploy`                | `#2673`       |
| SIT smoke test            | `home-loan-sit-run-smoke`             | `#3235`       |
| QA deployment             | `home-loan-qa-deploy`                 | `#2225`       |

The build numbers such as `#3340`, `#3410`, `#2673`, etc. identify individual Jenkins executions.

They are **not the same as Git commit IDs**.

---

# Common New Joiner Questions

### What happens when code is merged into master?

The Jenkins build pipeline is triggered for the service.

For example:

```text
home-loan-home-loan-ui-build #3340
```

Jenkins then runs the configured build, quality, and security checks.

### What happens after the master build succeeds?

The successful build proceeds through the SIT deployment flow.

The version manifest is generated and the application is deployed to SIT.

### Does BUILD SUCCESSFUL mean the application is deployed to SIT?

No.

`BUILD SUCCESSFUL` means the Jenkins **build stage** completed successfully.

SIT deployment is a separate stage.

### Why is the version manifest generated?

It generates the version and deployment metadata required for the application release.

### Can SIT deployment be triggered manually?

Yes.

The SIT deployment can also be triggered directly by manually running the SIT Deploy pipeline.

### What happens after SIT deployment succeeds?

Automated SIT smoke tests are triggered.

### What is the purpose of the smoke test?

It performs basic validation to check that the application deployed to SIT is working correctly.

### Can the build move to QA if smoke tests fail?

No.

The documented flow requires the SIT smoke tests to be successful before the build is promoted to QA.

### What does "SMOKE TESTS GREEN" mean?

It means the SIT smoke tests completed successfully and the build can proceed to the QA promotion step.

### What happens after smoke tests pass?

The build is promoted to QA and the QA deployment pipeline runs.

### What is the difference between SIT deployment and QA deployment?

They deploy the application to different environments.

The master flow first deploys to **SIT**, validates the application using smoke tests, and then proceeds to **QA**.

---

# Troubleshooting Mental Model

When something goes wrong, first identify **which stage failed**.

```text id="x4q9lm"
Build failed?
    ↓
Check Build pipeline

Manifest failed?
    ↓
Check Version Manifest pipeline

SIT deployment failed?
    ↓
Check SIT Deploy pipeline

Smoke test failed?
    ↓
Check SIT Smoke Test pipeline

QA deployment issue?
    ↓
Check QA Deploy pipeline
```

Do not treat all failures as the same type of failure.

For example:

* A build failure is different from a SIT deployment failure.
* A SIT deployment failure is different from a smoke test failure.
* A smoke test failure is different from a QA deployment failure.

The first step in troubleshooting should therefore be to identify **which Jenkins pipeline and stage failed**.

---

# Rules for Answering Master Branch Flow Questions

When answering a new joiner's question about the master branch flow:

* Start with the simple end-to-end flow.
* Explain each Jenkins pipeline in the order it runs.
* Clearly distinguish **build**, **deployment**, and **testing**.
* Explain that `BUILD SUCCESSFUL` refers to the build stage, not the entire deployment process.
* Explain that SIT deployment happens after the successful master build.
* Explain that smoke tests validate the application after it is deployed to SIT.
* Clearly state that smoke tests must pass before promotion to QA.
* Explain that SIT deployment can also be manually triggered.
* Use the documented Jenkins pipeline names and examples when relevant.
* When troubleshooting, first identify which stage/pipeline failed.
* Do not assume additional behavior that is not documented in this context.
