# Sandbox, Sandbox1, and UAT Deployment

## Overview

This document explains how to deploy a service to the following environments:

* Sandbox
* SandboxNew
* Sandbox1
* UAT

Deployments are performed through the appropriate Jenkins deployment pipeline using:

```text id="7m2qkx"
BUILD WITH PARAMETERS
```

The important concept for a new joiner is:

> **Each environment has its own deployment pipeline and expected upstream environment. UAT is different from Sandbox/Sandbox1 because UAT also requires a versioning option.**

The basic flow is:

```text id="n8c4vz"
Select Environment
        ↓
Select Deployment Pipeline
        ↓
BUILD WITH PARAMETERS
        ↓
Select Upstream
        ↓
For UAT → Select Versioning
        ↓
Run Deployment
```

---

# 1. Deployment Pipelines

To deploy to these environments, use the corresponding deployment pipeline and select **Build With Parameters**.

The deployment targets covered here are:

```text id="p3x7qa"
Sandbox
SandboxNew
Sandbox1
UAT
```

The exact Jenkins pipeline name follows the service/environment deployment naming convention.

For example:

```text id="w6k2mz"
Service Name-sit-deploy
Service Name-qa-deploy
Service Name-perf-deploy
Service Name-prodsandbox-deploy
```

For Sandbox, Sandbox1, or UAT, use the corresponding deployment pipeline available for that service.

---

# 2. Sandbox Deployment

## Upstream

The upstream environment for **Sandbox** is:

```text id="b9t4yc"
qa
```

The simplified deployment flow is:

```text id="v2k7np"
QA
 ↓
Build / Version
 ↓
Sandbox
```

Therefore, when deploying to Sandbox, the documented upstream is `qa`.

### Important distinction

`qa` is the **upstream/source**.

`Sandbox` is the **target environment**.

So:

```text id="m5x8qd"
upstream_environment = qa
target = Sandbox
```

does **not** mean that the application is being deployed to QA.

It means the build/version is being taken from the QA upstream and deployed to Sandbox.

---

# 3. SandboxNew

SandboxNew is also a supported deployment target.

The deployment should be performed using the corresponding SandboxNew deployment pipeline and **Build With Parameters**.

The provided context does not specify a separate upstream value for `SandboxNew`.

Therefore, do not assume an upstream value for SandboxNew unless it is explicitly defined in the service's Jenkins configuration or additional documentation.

### Important

Do not automatically assume:

```text
SandboxNew → QA
```

just because Sandbox uses `qa`.

The documented upstream value `qa` is specifically given for **Sandbox**.

---

# 4. Sandbox1 Deployment

## Upstream

The upstream environment for **Sandbox1** is:

```text id="c7v3xm"
qa1
```

The simplified flow is:

```text id="j4q8rs"
QA1
 ↓
Build / Version
 ↓
Sandbox1
```

Therefore:

```text id="k2n6wp"
upstream_environment = qa1
target = Sandbox1
```

Again, `qa1` is the upstream/source and `Sandbox1` is the target.

---

# 5. UAT Deployment

UAT has two possible upstream environments:

* `sandbox`
* `sandbox1`

Therefore, the UAT deployment can take its build/version from either of these documented upstream environments.

The flow is:

```text id="r8m3yk"
Sandbox ────┐
            ├──→ UAT
Sandbox1 ───┘
```

This means:

```text id="x5q9vc"
upstream_environment = sandbox
```

means:

> Use Sandbox as the upstream source for the UAT deployment.

Whereas:

```text id="d3k7mz"
upstream_environment = sandbox1
```

means:

> Use Sandbox1 as the upstream source for the UAT deployment.

---

# 6. UAT Versioning

UAT deployment has an additional **VERSIONING** parameter.

The available values are:

```text id="n7p2qx"
patching
minor
major
```

Versioning determines how the application release version is changed during the UAT deployment process.

The documented usage is:

| Versioning | Documented Use            |
| ---------- | ------------------------- |
| `patching` | Patch versioning          |
| `minor`    | Hotfix deployment         |
| `major`    | Master release deployment |

---

# 7. `patching`

`patching` is one of the available UAT versioning options.

The provided context identifies `patching` as a versioning option but does not define a specific deployment scenario for it.

Therefore, a new joiner should understand:

> `patching` controls patch-level versioning, but the exact circumstances in which it should be selected are not specified in this context.

Do not invent an additional use case for `patching`.

---

# 8. `minor`

The documented rule is:

> **Minor is for hotfix deployment.**

Therefore, when the UAT deployment is being performed as part of the documented hotfix flow, the versioning value is:

```text id="z4m8pt"
minor
```

The simplified model is:

```text id="q6v3kn"
Hotfix
   ↓
UAT Deployment
   ↓
Versioning = minor
```

---

# 9. `major`

The documented rule is:

> **Major is for Master release deployment.**

Therefore, when the UAT deployment is part of the documented master release flow, the versioning value is:

```text id="s8y2mr"
major
```

The simplified model is:

```text id="h5c9xq"
Master Release
      ↓
UAT Deployment
      ↓
Versioning = major
```

---

# 10. UAT Deployment Parameters Together

When deploying to UAT, the important inputs are:

### Upstream

Choose one of:

```text id="e7m3vz"
sandbox
sandbox1
```

### Versioning

Choose one of:

```text id="w4k8qp"
patching
minor
major
```

So conceptually:

```text id="j9r2mc"
UAT Deployment
      ↓
┌───────────────────┐
│ upstream          │
│ sandbox / sandbox1│
└─────────┬─────────┘
          ↓
┌───────────────────┐
│ versioning        │
│ patching/minor/   │
│ major             │
└─────────┬─────────┘
          ↓
      UAT Deploy
```

---

# 11. Complete Promotion Relationship

The documented upstream relationships can be visualized as:

```text id="v3n7kx"
QA
 ↓
Sandbox
 ↓
UAT
```

or:

```text id="q8m4zp"
QA1
 ↓
Sandbox1
 ↓
UAT
```

Therefore, UAT can receive its upstream from either:

```text id="c5r9tw"
Sandbox
    ↓
   UAT
```

or:

```text id="a2k6vn"
Sandbox1
    ↓
   UAT
```

This is an important relationship for understanding why UAT has two upstream choices.

---

# 12. Example: Sandbox Deployment

Suppose you want to deploy a service to Sandbox.

### Step 1 — Select the Sandbox deployment pipeline

Use the service's Sandbox deployment pipeline.

### Step 2 — Select Build With Parameters

```text id="m7x3qb"
BUILD WITH PARAMETERS
```

### Step 3 — Select the upstream

The documented upstream is:

```text id="t9k5cv"
qa
```

### Step 4 — Run the deployment

The flow is:

```text id="p4n8yz"
QA upstream
     ↓
Sandbox deployment
```

---

# 13. Example: Sandbox1 Deployment

For Sandbox1:

```text id="w2q7mk"
upstream_environment = qa1
```

The flow is:

```text id="d8v3rx"
QA1
 ↓
Sandbox1
```

---

# 14. Example: UAT Deployment from Sandbox

If UAT is being deployed using Sandbox as the upstream:

```text id="k9m4zp"
upstream_environment = sandbox
```

Then choose the appropriate versioning value.

For example, for a documented hotfix deployment:

```text id="n3x7vc"
versioning = minor
```

The conceptual flow is:

```text id="r5q8mt"
Sandbox
   ↓
UAT
   ↓
Versioning = minor
```

---

# 15. Example: UAT Master Release

For a master release deployment to UAT, the documented versioning value is:

```text id="y6p2kn"
major
```

The conceptual flow is:

```text id="c4m8vx"
Master Release
      ↓
UAT
      ↓
Versioning = major
```

The UAT upstream can be either:

```text id="s7q3wp"
sandbox
```

or:

```text id="h9m5zr"
sandbox1
```

depending on the deployment path being used.

---

# 16. Common New Joiner Questions

### How do I deploy to Sandbox?

Use the Sandbox deployment pipeline and select **Build With Parameters**.

The documented upstream for Sandbox is:

```text id="u4n8xm"
qa
```

### What is the Sandbox upstream?

`qa`.

### What is the Sandbox1 upstream?

`qa1`.

### What are the UAT upstream options?

UAT supports:

```text id="p6r2yv"
sandbox
sandbox1
```

### Can UAT use Sandbox1 as its upstream?

Yes.

`Sandbox1` is one of the documented UAT upstream options.

### What is the difference between upstream and target?

The **upstream** is the source from which the build/version is taken.

The **target** is the environment where the application is being deployed.

Example:

```text id="b7k3qz"
upstream = sandbox
target = UAT
```

means:

> Take the build/version from Sandbox and deploy it to UAT.

### What versioning options are available for UAT?

The documented options are:

* `patching`
* `minor`
* `major`

### Which versioning should be used for a hotfix?

The documented rule says:

```text id="x5m8qn"
minor
```

is used for hotfix deployment.

### Which versioning should be used for a Master release?

The documented rule says:

```text id="v3q7kc"
major
```

is used for Master release deployment.

### What is `patching` used for?

`patching` is a documented UAT versioning option, but the provided context does not specify a particular deployment scenario for it.

Do not assume a use case that is not documented.

### What is the upstream for SandboxNew?

The provided context does not specify one.

The service's Jenkins pipeline configuration should be checked rather than assuming that it is the same as Sandbox.

---

# 17. Quick Reference

| Target         | Upstream              | Additional Information               |
| -------------- | --------------------- | ------------------------------------ |
| **Sandbox**    | `qa`                  | Uses QA as upstream                  |
| **SandboxNew** | Not specified         | Check service pipeline configuration |
| **Sandbox1**   | `qa1`                 | Uses QA1 as upstream                 |
| **UAT**        | `sandbox`, `sandbox1` | Also requires versioning             |

### UAT Versioning

| Versioning | Use                                                  |
| ---------- | ---------------------------------------------------- |
| `patching` | Patch versioning; specific scenario not defined here |
| `minor`    | Hotfix deployment                                    |
| `major`    | Master release deployment                            |

---

# 18. Beginner Mental Model

Remember the environment relationships as:

```text id="z6k3rp"
           QA
           ↓
        Sandbox
           ↓
          UAT
```

and:

```text id="f8m2vx"
          QA1
           ↓
        Sandbox1
           ↓
          UAT
```

UAT can therefore have two upstream paths:

```text id="q4v9mt"
Sandbox ────┐
            ├──→ UAT
Sandbox1 ───┘
```

For UAT, remember one additional concept:

```text id="m7x2nc"
UAT Deployment
      +
Versioning
      ↓
patching / minor / major
```

And the two explicitly documented release mappings are:

```text id="r5k8qp"
Hotfix
  ↓
minor

Master Release
  ↓
major
```

---

# Rules for Answering Sandbox/UAT Deployment Questions

When answering a new joiner's question:

* First identify the **target environment**.
* Then identify the corresponding deployment pipeline.
* Explain that the deployment is started using **Build With Parameters**.
* Clearly distinguish the **upstream environment** from the **target environment**.
* For Sandbox, state that the documented upstream is `qa`.
* For Sandbox1, state that the documented upstream is `qa1`.
* For UAT, state that the documented upstream options are `sandbox` and `sandbox1`.
* Do not invent an upstream value for SandboxNew because it is not specified in this context.
* Explain UAT's `patching`, `minor`, and `major` versioning options.
* Clearly state that `minor` is used for the documented hotfix deployment.
* Clearly state that `major` is used for the documented Master release deployment.
* Do not invent a specific scenario for `patching`.
* If the exact parameter values for a particular service are unclear, refer to the service's Jenkins pipeline configuration rather than guessing.
* Do not assume that all services have identical deployment configuration.
