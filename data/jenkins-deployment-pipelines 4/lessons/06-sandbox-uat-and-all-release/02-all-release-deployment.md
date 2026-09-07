# All Release Deployment

## Overview

The **All Release Pipeline** is a Jenkins pipeline used to move a service through the release flow.

It can be used for:

* UAT deployment
* Complete production release

The pipeline follows a broader release process than the individual environment deployment pipelines.

The pipeline name follows this pattern:

```text id="q7m2vx"
Service-name-all-release-pipeline
```

For a new joiner, the most important distinction is:

> **Use the All Release Pipeline when you want to follow the complete release flow. If you only need to publish the changes to UAT, the pipeline is stopped after the UAT deployment starts.**

---

# 1. All Release Pipeline

The pipeline follows the naming convention:

```text id="m4x8kp"
Service-name-all-release-pipeline
```

This pipeline can be used for both:

```text id="y3n7qc"
UAT
```

and:

```text id="r5k9zw"
PROD
```

Unlike a pipeline such as:

```text id="Service-name-uat-deploy"
```

the All Release Pipeline is intended to handle the broader release process.

---

# 2. All Release Parameters

When the All Release Pipeline is started, use:

```text id="a8q3mn"
BUILD WITH PARAMETERS
```

The important parameter is:

```text id="p6v2xr"
Release
```

## `Release`

The `Release` parameter determines:

> **Where you want to pick your changes from.**

The available values are:

* `Sandbox`
* `sandbox1`

Therefore:

```text id="k9m4zt"
Release = Sandbox
```

means the changes are picked from **Sandbox**.

Similarly:

```text id="w7c3qp"
Release = sandbox1
```

means the changes are picked from **Sandbox1**.

---

# 3. Release Parameter Mental Model

Think of the `Release` parameter as selecting the starting point for the release.

```text id="f2x8mv"
Release Parameter
        ↓
 ┌─────────────┐
 │             │
Sandbox     sandbox1
 │             │
 └──────┬──────┘
        ↓
   All Release Flow
```

The important distinction is:

> `Release` tells the pipeline **where to pick the changes from**.

It is not the same as selecting the final deployment environment.

---

# 4. UAT Deployment Through All Release

UAT can be deployed using the All Release Pipeline.

If the requirement is:

> **"I only want to publish my changes to UAT."**

then the All Release Pipeline should not be allowed to continue through the complete production release process.

The documented process is:

1. Start the All Release Pipeline.
2. Select the required `Release` value.
3. Let the pipeline proceed.
4. Wait until the **UAT deployment starts**.
5. **Abort the pipeline once the UAT deployment starts.**

The flow is:

```text id="n5q8yc"
All Release Pipeline
        ↓
Select Release
        ↓
Release from Sandbox / sandbox1
        ↓
UAT Deployment Starts
        ↓
ABORT PIPELINE
```

### Why abort?

The All Release Pipeline is capable of continuing beyond UAT toward the later release stages.

If the requirement is only to publish the changes to UAT, the pipeline should be stopped after UAT deployment starts.

The key rule is:

> **For UAT-only deployment through All Release, abort the pipeline once UAT deployment starts.**

---

# 5. Important UAT Behavior

A new joiner may wonder:

> "Why am I aborting a successful pipeline?"

The reason is that the objective is **only UAT deployment**, while the All Release Pipeline can continue through the larger release process.

Therefore, in this specific scenario:

```text id="x4m7kp"
UAT deployment starts
        ↓
Stop / Abort All Release Pipeline
```

The abort is intentional when the goal is UAT-only publication.

---

# 6. Complete PROD Release

If the requirement is:

> **"I want to completely deploy the release to PROD."**

then the All Release Pipeline should be allowed to continue.

The flow is:

```text id="c8q3mv"
All Release Pipeline
        ↓
Release from Sandbox / sandbox1
        ↓
UAT
        ↓
Further release stages
        ↓
PREPROD
        ↓
Infra Approval
        ↓
PROD
```

Do **not** abort the pipeline at the UAT stage when the objective is a complete production release.

---

# 7. PREPROD and PROD

During a complete production release, the pipeline eventually reaches **PREPROD**.

At this point:

> **Infra team approval is required before deployment to PROD.**

The simplified flow is:

```text id="j6v9rx"
All Release
     ↓
UAT
     ↓
...
     ↓
PREPROD
     ↓
Infra Team Approval
     ↓
PROD
```

Therefore, reaching PREPROD does not mean that the application is automatically deployed to PROD.

There is an approval step before the production deployment.

---

# 8. UAT-Only vs Complete PROD Release

This is the most important difference to remember.

| Requirement                 | What to do                                   |
| --------------------------- | -------------------------------------------- |
| Publish changes to UAT only | Abort All Release once UAT deployment starts |
| Complete release to PROD    | Continue the All Release Pipeline            |
| Pipeline reaches PREPROD    | Infra team approval is required before PROD  |

The mental model is:

```text id="r3m8vz"
                    All Release
                         ↓
                Sandbox / sandbox1
                         ↓
                        UAT
                         ↓
              ┌──────────┴──────────┐
              │                     │
          UAT only              PROD release
              │                     │
            ABORT                 Continue
                                    ↓
                                  PREPROD
                                    ↓
                            Infra Approval
                                    ↓
                                   PROD
```

---

# 9. Example — UAT Only

Suppose a developer wants to publish changes to UAT but does not want to perform a complete production release.

### Step 1

Open:

```text id="x5q7mn"
Service-name-all-release-pipeline
```

### Step 2

Select:

```text id="n8c2vr"
BUILD WITH PARAMETERS
```

### Step 3

For `Release`, select the required source:

```text id="p4m7zx"
Sandbox
```

or:

```text id="w6k3qt"
sandbox1
```

### Step 4

Start the pipeline.

### Step 5

Allow the pipeline to proceed until the UAT deployment starts.

### Step 6

Abort the All Release Pipeline.

The result is:

```text id="v9m2kc"
Changes
  ↓
UAT Deployment
  ↓
STOP
```

---

# 10. Example — Complete PROD Release

If the objective is a complete production release:

### Step 1

Open:

```text id="j4x8qp"
Service-name-all-release-pipeline
```

### Step 2

Select:

```text id="s7m3vz"
BUILD WITH PARAMETERS
```

### Step 3

Select the appropriate `Release` value:

```text id="d5q9km"
Sandbox
```

or:

```text id="h2x6rp"
sandbox1
```

### Step 4

Start the pipeline.

### Step 5

Allow the pipeline to continue through UAT and the subsequent release stages.

### Step 6

When the pipeline reaches PREPROD, Infra team approval is required.

### Step 7

After the required approval, the release can proceed to PROD.

The simplified flow is:

```text id="k8m4yx"
Release Source
      ↓
UAT
      ↓
Further Release Stages
      ↓
PREPROD
      ↓
Infra Approval
      ↓
PROD
```

---

# 11. All Release vs Individual Deployment Pipeline

There are two different ways to think about deployment.

### Individual environment deployment

An individual deployment pipeline is focused on a specific environment.

For example:

```text id="b7q3mv"
Service-name-uat-deploy
```

is used for UAT deployment.

### All Release Pipeline

The All Release Pipeline handles the broader release flow:

```text id="f9x2kp"
Service-name-all-release-pipeline
```

It can be used to reach UAT and, when allowed to continue, proceed toward PROD.

The important distinction is:

> **Individual deployment pipelines target a specific environment, while the All Release Pipeline follows the broader release process.**

---

# 12. Common New Joiner Questions

### What is the All Release Pipeline?

It is a Jenkins pipeline used for the broader release flow and can be used for UAT as well as complete PROD deployment.

### What is the pipeline name?

The naming pattern is:

```text id="z6m3qx"
Service-name-all-release-pipeline
```

### What parameter do I provide?

Use **Build With Parameters** and provide the `Release` parameter.

### What values can `Release` have?

The documented values are:

```text id="t8q4mk"
Sandbox
sandbox1
```

### What does `Release` mean?

It specifies where you want to pick your changes from.

### Can I use All Release to deploy only to UAT?

Yes.

When using All Release for UAT-only deployment, allow the pipeline to reach the UAT deployment and then abort it once UAT deployment starts.

### Why do I need to abort the pipeline for UAT?

Because the All Release Pipeline can continue into the later release process. If you only want the changes published to UAT, the documented process is to stop the pipeline after UAT deployment starts.

### Should I abort All Release if I want to deploy to PROD?

No.

If you want the complete release to PROD, allow the pipeline to continue.

### What happens when the pipeline reaches PREPROD?

Infra team approval is required before the release can be deployed to PROD.

### Is UAT deployment the same as PROD deployment?

No.

UAT is an earlier stage in the release flow. PROD is the final production deployment and requires the documented approval at PREPROD.

### What happens if I stop the pipeline before UAT deployment starts?

The provided context only specifies that the pipeline should be aborted **once UAT deployment starts** for a UAT-only release. Do not assume that aborting it earlier will complete the UAT deployment.

---

# 13. Troubleshooting / Decision Guide

When someone asks what to do with an All Release Pipeline, first determine the goal.

### Goal: UAT only

```text id="m5x9qc"
Start All Release
      ↓
Select Sandbox / sandbox1
      ↓
Wait for UAT deployment
      ↓
UAT deployment starts
      ↓
ABORT
```

### Goal: Complete PROD release

```text id="r7k2vn"
Start All Release
      ↓
Select Sandbox / sandbox1
      ↓
Continue
      ↓
UAT
      ↓
...
      ↓
PREPROD
      ↓
Infra Approval
      ↓
PROD
```

This distinction should be checked before advising someone to abort or continue the pipeline.

---

# 14. Quick Reference

### Pipeline

```text id="c4m8xp"
Service-name-all-release-pipeline
```

### Start

```text id="y7q3kn"
BUILD WITH PARAMETERS
```

### Release parameter

```text id="p9v5rz"
Sandbox
sandbox1
```

### UAT-only flow

```text id="h6m2qx"
All Release
   ↓
UAT deployment starts
   ↓
Abort
```

### Complete PROD flow

```text id="w3k8mv"
All Release
   ↓
UAT
   ↓
...
   ↓
PREPROD
   ↓
Infra approval
   ↓
PROD
```

---

# Rules for Answering All Release Questions

When answering a new joiner's question:

* Explain that the **All Release Pipeline** is different from an individual environment deployment pipeline.
* Use the documented pipeline naming pattern: `Service-name-all-release-pipeline`.
* Explain that the pipeline is started using **Build With Parameters**.
* Explain `Release` as the source from which changes are picked.
* Only use the documented `Release` values: `Sandbox` and `sandbox1`.
* If the goal is **UAT only**, clearly state that the pipeline should be aborted once UAT deployment starts.
* If the goal is a **complete PROD release**, clearly state that the pipeline should continue.
* Explain that reaching PREPROD does not mean PROD deployment is automatic.
* Clearly state that **Infra team approval is required at PREPROD before PROD deployment**.
* Do not tell a user to abort the pipeline for a complete PROD release.
* Do not assume additional release stages or approval requirements that are not documented in this context.
* If the user asks what happens at a stage that is not described here, state that the available context does not define that behavior rather than guessing.
