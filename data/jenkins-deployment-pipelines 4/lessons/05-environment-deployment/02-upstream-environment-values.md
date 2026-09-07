# Upstream Environment Values

## Overview

When running a deployment pipeline, the `upstream_environment` parameter specifies the **environment/source from which the build is taken**.

It is important to understand that:

> **`upstream_environment` tells the deployment pipeline where the build/version comes from. It does not by itself specify where the application will be deployed.**

The **deployment pipeline** determines the target environment.

For example:

```text id="v8k2mx"
Service Name-qa-deploy
        ↓
Target Environment = QA

upstream_environment = sit
        ↓
Build comes from SIT upstream
```

So:

```text id="4n6zqp"
Target Environment
        ≠
Upstream Environment
```

---

# 1. SIT Upstream Values

For **SIT**, the upstream has **build only**.

The available build is the:

> **Latest master branch build**

The simplified flow is:

```text id="f5q7td"
Master Branch
      ↓
Latest Master Build
      ↓
SIT Deployment
```

### Important

When deploying to SIT, the upstream source is based on the latest successful master branch build available for the service.

The documented upstream behavior for SIT does not list other environments such as QA or PERF.

---

# 2. SIT1 Upstream Values

For **SIT1**, the upstream also has **build only**.

The build used is:

> **Latest master branch build**

The simplified flow is:

```text id="s7m3kw"
Master Branch
      ↓
Latest Master Build
      ↓
SIT1 Deployment
```

Therefore, the documented SIT1 upstream behavior is similar to SIT:

```text id="h2q8nd"
SIT
 └── Build
      └── Latest master branch build

SIT1
 └── Build
      └── Latest master branch build
```

---

# 3. QA Upstream Values

For **QA**, the available upstream environments are:

* `sit`
* `qa`

This means the QA deployment pipeline can use a build from either of these documented upstream sources.

Conceptually:

```text id="j9x4vp"
        ┌── SIT
        │
        └── QA
             ↓
       QA Deployment
```

For example:

```text id="u2w6ra"
upstream_environment = sit
```

means the QA deployment is using the SIT upstream source.

Similarly:

```text id="c8p5yl"
upstream_environment = qa
```

means the QA deployment is using the QA upstream source.

### Important

The fact that `qa` is available as an upstream value does **not** mean that the target environment changes.

If you are running:

```text id="p3k7mt"
Service Name-qa-deploy
```

the target is still **QA**.

The `upstream_environment` value only identifies the source.

---

# 4. QA1 Upstream Values

For **QA1**, the documented upstream environments are:

* `sit1`
* `prod`

The simplified flow is:

```text id="r4m8xz"
SIT1 ──────┐
           ├──→ QA1 Deployment
PROD ──────┘
```

Therefore, when using the QA1 deployment pipeline, the available upstream source can be:

```text id="k6q1ws"
upstream_environment = sit1
```

or:

```text id="d9v3hb"
upstream_environment = prod
```

---

# 5. PERF Upstream Values

PERF has a broader set of upstream environment options.

The documented upstream values are:

* `sit`
* `qa`
* `perf`
* `sandbox`
* `sandboxNew`
* `prod`

Some services also have:

* `SIT1`
* `QA1`
* `prodSandBox`

The simplified model is:

```text id="w5j2nc"
SIT
QA
PERF
Sandbox
SandboxNew
PROD
  │
  ├───────────────┐
  │               │
  ↓               ↓
PERF          Some services:
Deployment    SIT1 / QA1 / prodSandBox
```

### Important PERF behavior

**PERF does not have regression testing.**

This is an important difference to remember when compared with environments where regression testing may be enabled.

---

# 6. Complete Upstream Reference

The documented upstream values can be summarized as follows:

| Target Environment       | Available Upstream Values                            |
| ------------------------ | ---------------------------------------------------- |
| **SIT**                  | Build only → Latest master branch build              |
| **SIT1**                 | Build only → Latest master branch build              |
| **QA**                   | `sit`, `qa`                                          |
| **QA1**                  | `sit1`, `prod`                                       |
| **PERF**                 | `sit`, `qa`, `perf`, `sandbox`, `sandboxNew`, `prod` |
| **PERF — some services** | Also `SIT1`, `QA1`, `prodSandBox`                    |

---

# 7. Understanding "Build Only"

For SIT and SIT1, the upstream is described as **build only**.

This means the upstream source is based on a Jenkins build rather than another deployment environment.

The documented build is:

> **Latest master branch build**

So the mental model is:

```text id="a7f4qc"
Master
  ↓
Latest Build
  ↓
SIT / SIT1
```

This is different from an upstream value such as:

```text id="m8r2yk"
upstream_environment = sit
```

where the deployment process is using SIT as the upstream source.

---

# 8. Target Environment vs Upstream Environment

This is the most important concept in this context.

### Target environment

The target environment is determined by the deployment pipeline.

For example:

```text id="t6n9pz"
Service Name-sit-deploy
        ↓
Target = SIT

Service Name-qa-deploy
        ↓
Target = QA

Service Name-perf-deploy
        ↓
Target = PERF
```

### Upstream environment

The upstream environment identifies where the build/version comes from.

For example:

```text id="e3c7mv"
Target = QA
Upstream = SIT
```

means:

> Deploy to QA using the build from the SIT upstream source.

It does **not** mean:

> Deploy to SIT.

---

# 9. Example: QA Deployment

Suppose you want to deploy a service to QA.

First, select:

```text id="j2v5qs"
Service Name-qa-deploy
```

This establishes:

```text id="x8c4mk"
Target Environment = QA
```

Then, in **Build With Parameters**, you select an upstream environment.

For example:

```text id="q6t1zw"
upstream_environment = sit
```

The resulting meaning is:

```text id="r7m3dn"
SIT
 ↓
Build/Version
 ↓
QA Deployment
```

The target is still QA.

---

# 10. Example: QA1 Deployment

For QA1:

```text id="s9k4vb"
Service Name-qa1-deploy
```

The documented upstream values are:

```text id="w3x7fp"
sit1
prod
```

So conceptually:

```text id="m6q2ht"
SIT1 ──→
        QA1
PROD ─→
```

---

# 11. Example: PERF Deployment

For PERF:

```text id="n4z8jc"
Service Name-perf-deploy
```

The documented upstream values include:

```text id="c7m2yx"
sit
qa
perf
sandbox
sandboxNew
prod
```

Some services additionally support:

```text id="p5r9kw"
SIT1
QA1
prodSandBox
```

Therefore, the available options can vary slightly between services.

### Important

Do not assume that every service has every PERF upstream option.

The documented rule is:

> **Some services have the additional `SIT1`, `QA1`, and `prodSandBox` upstream values.**

---

# 12. Service-Specific Difference

One important exception is:

> In some services, `prodsanbox` has been changed to `QA1`.

Therefore, a new joiner should not assume that the upstream value is identical across every service.

For a specific service, check the options available in that service's deployment pipeline.

Conceptually:

```text id="u8c3ml"
Service A
    ↓
prodsanbox

Service B
    ↓
QA1
```

The exact available value depends on the service's Jenkins pipeline configuration.

---

# 13. PERF and Regression Testing

PERF has a specific documented behavior:

> **PERF does not have regression testing.**

Therefore, when discussing deployment to PERF, do not assume that regression testing works in the same way as other environments.

The important point is:

```text id="d5j7qn"
PERF
 ↓
No regression testing
```

This should be considered when explaining the `enableRegressionTests` parameter for PERF deployments.

---

# 14. Easy Way to Remember the Values

A new joiner can remember the upstream relationships like this:

```text id="b2m7vx"
SIT
 ↓
Latest Master Build

SIT1
 ↓
Latest Master Build

QA
 ↓
SIT / QA

QA1
 ↓
SIT1 / PROD

PERF
 ↓
SIT / QA / PERF / Sandbox / SandboxNew / PROD
 + some services: SIT1 / QA1 / prodSandBox
```

---

# 15. Common New Joiner Questions

### What is `upstream_environment`?

It identifies the environment/source from which the build/version is taken for the deployment.

### Does `upstream_environment` decide where my application is deployed?

No.

The deployment pipeline determines the target environment.

For example:

```text id="f8w3kc"
Service Name-qa-deploy
```

targets QA regardless of which valid upstream environment is selected.

### What is the SIT upstream?

SIT has **build only**, using the latest master branch build.

### What is the SIT1 upstream?

SIT1 has **build only**, using the latest master branch build.

### What are the QA upstream values?

QA supports:

```text id="q5x9bm"
sit
qa
```

### What are the QA1 upstream values?

QA1 supports:

```text id="k3v7rt"
sit1
prod
```

### What are the PERF upstream values?

PERF supports:

```text id="m8c2zw"
sit
qa
perf
sandbox
sandboxNew
prod
```

Some services additionally support:

```text id="r6y4np"
SIT1
QA1
prodSandBox
```

### Does every service have the same PERF upstream values?

Not necessarily.

Some services have the additional `SIT1`, `QA1`, and `prodSandBox` options.

### Does PERF have regression testing?

No. The documented PERF behavior says that PERF does not have regression testing.

### Why is `prod` an upstream for QA1?

According to the documented pipeline configuration, `prod` is one of the available upstream values for QA1.

Do not infer a different meaning unless additional service-specific documentation provides it.

### What if I see `QA1` instead of `prodsanbox`?

Some services have changed `prodsanbox` to `QA1`.

The exact option available depends on the service's pipeline configuration.

---

# 16. Troubleshooting Upstream Selection

If someone asks:

> "Which upstream environment should I select?"

Do not answer based only on the target environment name.

First identify:

1. **Which target environment are you deploying to?**
2. **Which deployment pipeline are you using?**
3. **Which upstream values are available in that pipeline?**
4. **Is this service one of the services with special upstream configuration?**

Then select from the documented options for that pipeline.

For example:

```text id="z1q6mc"
Target = QA
     ↓
Use QA deployment pipeline
     ↓
Available upstream:
     sit
     qa
```

For QA1:

```text id="y7k3pf"
Target = QA1
     ↓
Use QA1 deployment pipeline
     ↓
Available upstream:
     sit1
     prod
```

For PERF:

```text id="h4m9xs"
Target = PERF
     ↓
Use PERF deployment pipeline
     ↓
Available upstream:
     sit
     qa
     perf
     sandbox
     sandboxNew
     prod
     + possibly SIT1 / QA1 / prodSandBox
```

---

# Rules for Answering Upstream Environment Questions

When answering a new joiner's question:

* First identify the **target deployment environment**.
* Explain that `upstream_environment` represents the **source/upstream environment**, not the deployment target.
* For SIT, state that the upstream is build-only and uses the latest master branch build.
* For SIT1, state that the upstream is build-only and uses the latest master branch build.
* For QA, use only the documented values: `sit` and `qa`.
* For QA1, use only the documented values: `sit1` and `prod`.
* For PERF, use the documented values: `sit`, `qa`, `perf`, `sandbox`, `sandboxNew`, and `prod`.
* Mention that some services additionally have `SIT1`, `QA1`, and `prodSandBox` for PERF.
* Remember that PERF does not have regression testing.
* Mention the service-specific `prodsanbox` → `QA1` change when relevant.
* Do not assume that every service has exactly the same upstream options.
* Do not invent an upstream value if it is not documented.
* If the user asks which value they should select for a specific service and the available options are not provided, explain the documented options and state that the service's actual Jenkins configuration should be checked.
* Do not infer business meaning from an upstream value beyond what is documented.
