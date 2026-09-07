# Seed Deployment Overview and Build

## 1. What is Seed?

**Seed** contains application data and configuration that can be changed without redeploying and restarting the service.

It can contain:

* **Feature toggles**
* **Master data**
* **Product-specific configuration**
* **Template data**

The important idea for a new joiner is:

> **Seed contains data/configuration that can be updated independently of the service deployment, without requiring the service itself to be redeployed and restarted.**

For example, if a product-specific configuration needs to be changed, that change can be made through Seed rather than requiring a new application deployment, provided the particular data is managed through Seed.

---

# 2. What Kind of Data Does Seed Contain?

### Feature Toggles

Feature toggles control whether specific functionality or features are enabled or disabled.

### Master Data

Master data is application data that is required by the service and can be managed independently of the application code.

### Product-Specific Configuration

Configuration that is specific to a particular product can be maintained in Seed.

### Template Data

Templates required by the application can also be stored in Seed.

---

# 3. Why is Seed Useful?

Normally, changing application code requires a new build and deployment.

Seed is useful for data/configuration that is designed to be changed independently.

The basic idea is:

```text
Application Code
      ↓
Build
      ↓
Deploy Service
```

whereas Seed-related data can be changed without requiring:

```text
New Service Deployment
        +
Service Restart
```

Therefore:

> **Seed separates certain changeable application data/configuration from the service deployment lifecycle.**

---

# 4. Seed Build Pipeline

The Seed build is created using:

```text
Maximus-seed-build
```

This is the Jenkins pipeline used to build Seed.

The pipeline can build Seed from different branches, including:

* Specific branch
* Release branch
* Development branch
* Master branch

Therefore, when working with Seed, the branch being built is important because different environments use different branches.

---

# 5. Seed Branch by Environment

The Seed branch used depends on the target environment.

| Environment | Seed Branch        |
| ----------- | ------------------ |
| SIT         | Development branch |
| QA          | Release branch     |
| Sandbox     | Release branch     |
| SandboxNew  | Release branch     |
| UAT         | Release branch     |
| PROD        | Master branch      |

The overall mapping is:

```text
SIT
 ↓
Development branch

QA
 ↓
Release branch

Sandbox
 ↓
Release branch

SandboxNew
 ↓
Release branch

UAT
 ↓
Release branch

PROD
 ↓
Master branch
```

---

# 6. SIT Seed

For the **SIT** environment:

```text
Environment: SIT
Seed branch: Development
```

Therefore, when Seed needs to be built for SIT, the **Development branch** is used.

```text
Development branch
        ↓
Maximus-seed-build
        ↓
SIT
```

---

# 7. QA Seed

For the **QA** environment:

```text
Environment: QA
Seed branch: Release
```

The Release branch is used to build Seed for QA.

```text
Release branch
       ↓
Maximus-seed-build
       ↓
QA
```

---

# 8. Sandbox Seed

For the **Sandbox** environment:

```text
Environment: Sandbox
Seed branch: Release
```

The Release branch is used.

```text
Release branch
       ↓
Maximus-seed-build
       ↓
Sandbox
```

---

# 9. SandboxNew Seed

For the **SandboxNew** environment:

```text
Environment: SandboxNew
Seed branch: Release
```

The Release branch is used.

```text
Release branch
       ↓
Maximus-seed-build
       ↓
SandboxNew
```

---

# 10. UAT Seed

For the **UAT** environment:

```text
Environment: UAT
Seed branch: Release
```

The Release branch is used.

```text
Release branch
       ↓
Maximus-seed-build
       ↓
UAT
```

---

# 11. PROD Seed

For the **PROD** environment:

```text
Environment: PROD
Seed branch: Master
```

The Master branch is used for PROD.

```text
Master branch
      ↓
Maximus-seed-build
      ↓
PROD
```

The important distinction is:

> **PROD uses the Master branch, while SIT uses the Development branch and QA/Sandbox/SandboxNew/UAT use the Release branch.**

---

# 12. Complete Seed Branch Mapping

The easiest way to remember the environment mapping is:

```text
                    SEED
                     │
             Maximus-seed-build
                     │
        ┌────────────┼─────────────┐
        │            │             │
   Development    Release        Master
        │            │             │
        ↓            ↓             ↓
       SIT      QA/Sandbox/       PROD
                SandboxNew/
                   UAT
```

More explicitly:

```text
Development branch
        ↓
       SIT


Release branch
        ↓
   ┌────┼──────┬──────────┐
   ↓    ↓      ↓          ↓
  QA  Sandbox SandboxNew  UAT


Master branch
        ↓
       PROD
```

---

# 13. Seed Build vs Service Build

A new joiner may confuse the Seed build with the normal service build.

They are different concepts.

### Service Build

A service build primarily builds the application/service from its source code.

Example:

```text
home-loan-home-loan-orchestrator-build
```

### Seed Build

The Seed build is specifically for Seed:

```text
Maximus-seed-build
```

Seed contains changeable data/configuration such as:

* Feature toggles
* Master data
* Product-specific configuration
* Template data

Therefore:

> **Do not assume that the normal service build pipeline is the Seed build pipeline. Seed has its own build pipeline.**

---

# 14. Example

Suppose a new joiner needs to work with Seed for UAT.

The documented mapping says:

```text
Target Environment = UAT
        ↓
Seed Branch = Release
        ↓
Pipeline = Maximus-seed-build
```

For PROD:

```text
Target Environment = PROD
        ↓
Seed Branch = Master
        ↓
Pipeline = Maximus-seed-build
```

For SIT:

```text
Target Environment = SIT
        ↓
Seed Branch = Development
        ↓
Pipeline = Maximus-seed-build
```

---

# 15. Common New Joiner Questions

### What is Seed?

Seed contains feature toggles, master data, product-specific configuration, and template data that can be updated without redeploying and restarting the service.

### What is the Seed build pipeline?

The Seed build pipeline is:

```text
Maximus-seed-build
```

### Which branches can the Seed pipeline build?

It can build:

* A specific branch
* Release branch
* Development branch
* Master branch

### Which Seed branch is used for SIT?

**Development branch.**

### Which Seed branch is used for QA?

**Release branch.**

### Which Seed branch is used for Sandbox?

**Release branch.**

### Which Seed branch is used for SandboxNew?

**Release branch.**

### Which Seed branch is used for UAT?

**Release branch.**

### Which Seed branch is used for PROD?

**Master branch.**

### Why doesn't PROD use the Release branch?

The documented environment mapping specifies that **PROD uses the Master branch**. Do not assume a different branch unless the project's process documentation says so.

### Does changing Seed always require a service restart?

No. The purpose of the documented Seed data is that it can be updated without redeploying and restarting the service.

### Does Seed contain application code?

This context describes Seed primarily in terms of feature toggles, master data, product-specific configuration, and template data. It does not specify that Seed contains the application's service code.

---

# 16. Quick Reference

| Item                | Value                |
| ------------------- | -------------------- |
| Seed build pipeline | `Maximus-seed-build` |
| SIT branch          | Development          |
| QA branch           | Release              |
| Sandbox branch      | Release              |
| SandboxNew branch   | Release              |
| UAT branch          | Release              |
| PROD branch         | Master               |

### One-line memory aid

> **SIT → Development, QA/Sandbox/SandboxNew/UAT → Release, PROD → Master.**

---

# Rules for Answering Seed Questions

When answering questions about Seed:

* Explain Seed as **changeable application data/configuration** that can be updated without redeploying and restarting the service.
* Mention the documented Seed contents when relevant:

    * Feature toggles
    * Master data
    * Product-specific configuration
    * Template data
* Use the exact Seed build pipeline name: `Maximus-seed-build`.
* Explain that the pipeline can build different branches.
* Always distinguish the **target environment** from the **branch used to build Seed**.
* For SIT, use **Development branch**.
* For QA, use **Release branch**.
* For Sandbox, use **Release branch**.
* For SandboxNew, use **Release branch**.
* For UAT, use **Release branch**.
* For PROD, use **Master branch**.
* Do not assume that a normal service build pipeline is the same as the Seed build pipeline.
* Do not invent additional Seed deployment steps that are not documented here.
* If a question asks about Seed behavior not covered in this context, clearly state that the available documentation does not specify it rather than guessing.
