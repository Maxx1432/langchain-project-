# Seed Environment Deployment

## 1. Overview

Seed data can be deployed to different environments such as:

* SIT
* SIT1
* QA
* QA1
* PERF
* SANDBOX
* SANDBOX1
* UAT
* PROD

The deployment process and upstream/source environment can differ depending on the target environment.

A key parameter used for Seed deployment is:

```text
isForceUpdate: True
```

When `isForceUpdate` is set to `True`, it is used when the requirement is to **update all the Seed data in the target environment**.

---

# 2. What Does `isForceUpdate: True` Mean?

The parameter is:

```text
isForceUpdate: True
```

It is used when you want to update **all the Seed data** in the target environment.

For the environments documented here, the following environments use:

```text
isForceUpdate: True
```

* SIT
* SIT1
* QA
* QA1
* PERF
* SANDBOX
* SANDBOX1

The available context does not define the exact internal implementation of a force update. Therefore, it should be understood as:

> **Use `isForceUpdate: True` when the intention is to update all Seed data in the target environment.**

---

# 3. Important Terminology

### Target Environment

The environment where you want the Seed data to be deployed.

Example:

```text
Target = QA
```

means the Seed data is being deployed to QA.

### Upstream

The environment/source from which the Seed data is taken or promoted.

For example:

```text
QA
Upstream = SIT
```

means QA uses SIT as its documented upstream source.

> **Do not confuse the upstream environment with the target environment.**

---

# 4. SIT Seed Deployment

For **SIT**:

```text
isForceUpdate: True
```

Use this when you want to update all the Seed data in the SIT environment.

The documented configuration is:

```text
Target Environment: SIT
isForceUpdate: True
```

No upstream environment is specified in this context for SIT.

---

# 5. SIT1 Seed Deployment

For **SIT1**:

```text
isForceUpdate: True
```

Use this when you want to update all the Seed data in the SIT1 environment.

The documented configuration is:

```text
Target Environment: SIT1
isForceUpdate: True
```

No upstream environment is specified in this context for SIT1.

---

# 6. QA Seed Deployment

For **QA**:

```text
isForceUpdate: True
```

Use this when you want to update all the Seed data in QA.

The upstream environment is:

```text
SIT
```

Therefore:

```text
SIT
 ↓
QA
```

The documented configuration is:

```text
Target Environment: QA
Upstream: SIT
isForceUpdate: True
```

---

# 7. QA1 Seed Deployment

For **QA1**:

```text
isForceUpdate: True
```

Use this when you want to update all the Seed data in QA1.

QA1 can use the following upstream environments:

* PROD
* QA
* SANDBOX1
* SANDBOX

Therefore, the documented upstream options are:

```text
PROD ──────┐
QA ────────┤
SANDBOX1 ──┤ → QA1
SANDBOX ───┘
```

The configuration is:

```text
Target Environment: QA1
Upstream: PROD / QA / SANDBOX1 / SANDBOX
isForceUpdate: True
```

---

# 8. PERF Seed Deployment

For **PERF**:

```text
isForceUpdate: True
```

Use this when you want to update all the Seed data in PERF.

The documented upstream environments are:

* PROD
* QA
* SANDBOX1
* SANDBOX

Therefore:

```text
PROD ──────┐
QA ────────┤
SANDBOX1 ──┤ → PERF
SANDBOX ───┘
```

The configuration is:

```text
Target Environment: PERF
Upstream: PROD / QA / SANDBOX1 / SANDBOX
isForceUpdate: True
```

---

# 9. SANDBOX Seed Deployment

For **SANDBOX**:

```text
isForceUpdate: True
```

Use this when you want to update all the Seed data in SANDBOX.

The upstream environment is:

```text
QA1
```

Therefore:

```text
QA1
 ↓
SANDBOX
```

The documented configuration is:

```text
Target Environment: SANDBOX
Upstream: QA1
isForceUpdate: True
```

---

# 10. SANDBOX1 Seed Deployment

For **SANDBOX1**:

```text
isForceUpdate: True
```

Use this when you want to update all the Seed data in SANDBOX1.

The documented upstream options are:

* QA1
* prod

Therefore:

```text
QA1 ────┐
        ├──→ SANDBOX1
prod ───┘
```

The documented configuration is:

```text
Target Environment: SANDBOX1
Upstream: QA1 / prod
isForceUpdate: True
```

---

# 11. UAT Seed Deployment

The documented information for **UAT** is:

> For SIT deployment on the UAT environment.

Therefore, the available context indicates that the UAT Seed deployment is associated with **SIT deployment on UAT**.

However, the source does **not** specify:

* An `isForceUpdate` value for UAT
* A specific UAT upstream parameter
* Exact Jenkins steps
* The complete UAT Seed deployment procedure

Do not infer these values from the other environments.

If someone asks for the exact UAT Seed deployment parameters, the correct response is:

> **The available Seed deployment documentation only specifies that this is for SIT deployment on the UAT environment; it does not define the exact parameters or upstream value.**

---

# 12. PROD Seed Deployment

PROD Seed deployment follows a different process from the environments above.

For PROD:

> **The Master branch is used.**

The team must first create a **Jira card in the Seed board**.

The Jira card should contain:

* The PR for Master
* Necessary details required for the release

The process is:

```text
Create Jira Card
       ↓
Create PR for Master
       ↓
PR Approval
       ↓
Deployment Day
       ↓
DB Team merges all PRs into Master
       ↓
Seed Deployment Pipeline Triggered
       ↓
PROD Seed Deployment
```

---

# 13. PROD Seed Deployment — Step by Step

### Step 1 — Create Jira Card

The team creates a Jira card in the **Seed board**.

The card should contain the required PR and necessary deployment details.

### Step 2 — Create PR for Master

The required Seed changes are raised through a PR targeting the **Master branch**.

### Step 3 — Get PR Approval

The PR must be approved before the deployment day.

### Step 4 — Deployment Day

On the day of deployment, the **DB team merges all approved PRs into the Master branch**.

### Step 5 — Trigger Seed Deployment

After the PRs are merged into Master, the Seed deployment pipeline is triggered for the PROD deployment.

The important rule is:

> **PROD Seed deployment uses the Master branch, and the DB team performs the Master merge on the deployment day after the required PR approvals are complete.**

---

# 14. Why PROD Is Different

The documented PROD process has additional coordination around the Master branch.

For the other environments, the documentation mainly describes:

```text
Target Environment
        ↓
Upstream
        ↓
isForceUpdate: True
```

For PROD, the process includes:

```text
Jira Card
    ↓
PR for Master
    ↓
Approval
    ↓
DB Team merges on deployment day
    ↓
Master
    ↓
Seed Deployment Pipeline
```

Therefore, a new joiner should not treat PROD Seed deployment as identical to QA, Sandbox, or other environment Seed deployment.

---

# 15. Complete Environment Mapping

The documented Seed deployment relationships are:

| Target Environment | Upstream                       | `isForceUpdate` | Special Process                    |
| ------------------ | ------------------------------ | --------------- | ---------------------------------- |
| SIT                | Not specified                  | `True`          | Update all Seed data               |
| SIT1               | Not specified                  | `True`          | Update all Seed data               |
| QA                 | SIT                            | `True`          | Update all Seed data               |
| QA1                | PROD / QA / SANDBOX1 / SANDBOX | `True`          | Update all Seed data               |
| PERF               | PROD / QA / SANDBOX1 / SANDBOX | `True`          | Update all Seed data               |
| SANDBOX            | QA1                            | `True`          | Update all Seed data               |
| SANDBOX1           | QA1 / prod                     | `True`          | Update all Seed data               |
| UAT                | Not fully specified            | Not specified   | SIT deployment on UAT              |
| PROD               | Master                         | Not specified   | Jira + PR approval + DB team merge |

---

# 16. Environment Flow

Based only on the documented upstream relationships, the following relationships are defined:

```text
SIT
 ↓
QA
 ↓
SANDBOX
```

and:

```text
QA1
 ├──→ SANDBOX
 └──→ SANDBOX1
```

QA1 can also use:

```text
PROD
QA
SANDBOX1
SANDBOX
```

as upstream options.

PERF can use:

```text
PROD
QA
SANDBOX1
SANDBOX
```

as upstream options.

Important:

> These upstream relationships should not automatically be interpreted as one mandatory linear promotion path. Some environments have multiple documented upstream choices.

---

# 17. Example — Updating All Seed Data in QA

Suppose a developer needs to update all Seed data in QA.

The documented configuration is:

```text
Target = QA
Upstream = SIT
isForceUpdate = True
```

Conceptually:

```text
SIT
 ↓
QA
 ↓
Update all Seed data
```

---

# 18. Example — Updating All Seed Data in SANDBOX

For SANDBOX:

```text
Target = SANDBOX
Upstream = QA1
isForceUpdate = True
```

Conceptually:

```text
QA1
 ↓
SANDBOX
 ↓
Update all Seed data
```

---

# 19. Example — PROD Seed Deployment

For PROD, the process is different:

```text
Seed Changes
     ↓
Jira Card
     ↓
PR for Master
     ↓
PR Approved
     ↓
Deployment Day
     ↓
DB Team merges PRs into Master
     ↓
Seed Deployment Pipeline
     ↓
PROD
```

A new joiner should not assume that they can simply select an environment and force-update PROD using the same process as QA or Sandbox.

---

# 20. Common New Joiner Questions

### What is `isForceUpdate`?

It is used when you want to update all the Seed data in the target environment.

### What value should I use for `isForceUpdate` when updating all Seed data?

Use:

```text
isForceUpdate: True
```

where this behavior is documented for the target environment.

### What is the upstream environment?

It identifies the documented source/upstream environment used for the Seed deployment.

### What is the upstream for QA?

```text
SIT
```

### What is the upstream for SANDBOX?

```text
QA1
```

### What are the upstream options for QA1?

```text
PROD
QA
SANDBOX1
SANDBOX
```

### What are the upstream options for PERF?

```text
PROD
QA
SANDBOX1
SANDBOX
```

### What are the upstream options for SANDBOX1?

```text
QA1
prod
```

### Which branch is used for PROD Seed deployment?

The **Master branch**.

### What needs to happen before PROD Seed deployment?

The team needs to create a Jira card in the Seed board with the PR for Master and the necessary details. The PR must be approved. On deployment day, the DB team merges all approved PRs into Master, after which the Seed deployment pipeline is triggered.

### Does UAT use `isForceUpdate: True`?

The provided documentation does not specify this. It only states that the UAT configuration is for SIT deployment on the UAT environment.

### Can I assume that every environment has the same upstream?

No. Each environment has its own documented upstream configuration.

### Can I assume an upstream value if it is not documented?

No. If the upstream is not specified, the available documentation is insufficient to determine it.

---

# 21. Quick Reference

### Force update

```text
isForceUpdate: True
```

Use when the requirement is to update **all Seed data** in the target environment.

### Upstream mapping

```text
QA      ← SIT

SANDBOX ← QA1

QA1     ← PROD / QA / SANDBOX1 / SANDBOX

PERF    ← PROD / QA / SANDBOX1 / SANDBOX

SANDBOX1 ← QA1 / prod
```

SIT and SIT1 have no upstream specified in this context.

### PROD

```text
Master branch
     ↓
Jira Card + PR
     ↓
PR Approval
     ↓
DB Team merges on deployment day
     ↓
Seed Deployment Pipeline
     ↓
PROD
```

---

# Rules for Answering Seed Environment Deployment Questions

When answering questions about Seed environment deployment:

* First identify the **target environment**.
* Clearly distinguish the target environment from its **upstream**.
* Explain `isForceUpdate: True` as the documented option for updating all Seed data.
* For SIT, state that `isForceUpdate: True` is used to update all Seed data.
* For SIT1, state that `isForceUpdate: True` is used to update all Seed data.
* For QA, use `SIT` as the upstream.
* For QA1, use only the documented upstream options: `PROD`, `QA`, `SANDBOX1`, and `SANDBOX`.
* For PERF, use only the documented upstream options: `PROD`, `QA`, `SANDBOX1`, and `SANDBOX`.
* For SANDBOX, use `QA1` as the upstream.
* For SANDBOX1, use `QA1` or `prod` as the documented upstream options.
* Do not invent the missing UAT upstream or `isForceUpdate` behavior.
* For PROD, explain the Master-branch process rather than applying the normal environment process.
* Mention the Jira Seed board, Master PR, approval, deployment-day DB team merge, and Seed deployment pipeline when explaining PROD.
* Do not assume that multiple upstream options mean that all of them must be used.
* Do not assume that the upstream relationships form one mandatory linear promotion path.
* Do not invent additional deployment steps, approvals, parameters, or automation behavior that are not documented here.
