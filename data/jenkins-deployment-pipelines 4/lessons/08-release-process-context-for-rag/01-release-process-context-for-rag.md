# Maximus Master Release

## 1. What is a Master Release?

A **Master Release** is the planned process used to release multiple Maximus products and shared services together through a controlled sequence of environments.

The purpose is to make sure that:

* All required products and services are included.
* The correct versions are deployed.
* Required testing is completed.
* Security checks are completed.
* Required approvals are obtained.
* Rollback is possible if something goes wrong.
* There is clear evidence showing that the release was tested and approved.

A Master Release is therefore not just **"deploying code to Production."**

It is a controlled process that starts with release planning and branch creation, continues through testing and environment freezes, and ends with Production deployment and release closure.

---

# 2. What is the Master Release Flow?

The standard Master Release flow is:

**SIT → QA → Sandbox → UAT → Pre-Prod → Production**

Each environment has a different purpose.

### SIT

SIT is an early validation environment where the release is tested after the release branches/builds are prepared.

The purpose is to make sure the release is working correctly before it moves further.

### QA

QA is used for more detailed validation, including:

* Functional testing
* Regression testing
* Backward compatibility checks

Once the QA environment is frozen, new feature changes are not normally accepted into the release. Only approved bug fixes can be added through the patch request process.

### Sandbox

Sandbox is the next stage after QA.

The release is promoted to Sandbox after the QA build is accepted. The release is then validated for journeys, contracts, and dependency behavior.

### UAT

UAT stands for **User Acceptance Testing**.

This is where Product Owners/business stakeholders validate the product from a business perspective.

The release needs either:

* Explicit UAT sign-off, or
* An approved risk acceptance.

### Pre-Prod

Pre-Prod is the environment immediately before Production.

Production readiness is checked here, including configuration, secrets, deployment sequence, rollback assets, vulnerabilities, and required approvals.

### Production

Production is the live environment.

The release is deployed only after the required release gates and approvals are completed.

After deployment, smoke tests and monitoring are performed.

The documented release sequence is mandatory and products cannot bypass the shared quality and approval requirements.

---

# 3. Why Does the Release Move Through Multiple Environments?

The release does not go directly from development to Production because each environment provides a different validation stage.

A simplified way to understand it is:

**SIT → Does the release work?**

↓

**QA → Does the release pass functional, regression, and compatibility testing?**

↓

**Sandbox → Does it work correctly with the required journeys and dependencies?**

↓

**UAT → Does the business/Product Owner accept it?**

↓

**Pre-Prod → Is everything ready for Production?**

↓

**Production → Deploy the approved release.**

The purpose is to identify problems before they reach Production.

---

# 4. What is a Release Train?

A **release train** is the group of Maximus products and shared services that are being released together as part of the same planned release.

The release process therefore needs to consider not only one service but also:

* Other products
* Shared services
* Dependencies
* Versions
* Configuration
* Security
* Testing
* Approvals
* Rollback

---

# 5. What are the Different Release Types?

There are three documented release types.

## Master Release

A planned release across multiple products and shared services.

Example:

```text id="m3x8q1"
76.0.0
```

## Hotfix Release

A release used for a critical bug, security issue, or compliance fix that needs to be released urgently.

Example:

```text id="v7k2p4"
76.1.0
```

Hotfix releases use the hotfix environment path:

**SIT1 → QA1 → Sandbox1 → UAT → Pre-Prod → Production**

## Planned Independent Release

A planned release for an isolated product scope that is not part of the Master Release train.

Example:

```text id="n5c9r2"
76.0.1
```

---

# 6. What is the Difference Between Master Release and Hotfix Release?

The main difference is the release path.

### Master Release

**SIT → QA → Sandbox → UAT → Pre-Prod → Production**

### Hotfix Release

**SIT1 → QA1 → Sandbox1 → UAT → Pre-Prod → Production**

A hotfix is intended for an urgent critical bug, security, or compliance fix.

---

# 7. What Happens Before the Release Starts?

Before the actual deployment process begins, the release needs to be planned and frozen.

This includes:

1. Confirming which products are part of the release.
2. Locking the release calendar slot.
3. Confirming the branch strategy.
4. Publishing the required SPOC matrix.
5. Enforcing the code cut-off.
6. Getting approval for any common-service cut-off exception.

The purpose of this step is to make sure everyone knows **what is being released and when**.

---

# 8. What is the Release Calendar?

The **release calendar** defines the planned schedule for the release.

Important release activities, including the branch cut, happen according to dates decided in the release calendar.

Therefore:

> **Branch cut does not happen whenever a developer wants to create a release branch. It happens on the stipulated date decided as part of the release calendar.**

The release calendar provides the schedule around which the release activities are coordinated.

---

# 9. What is Branch Cut?

**Branch cut** is the point at which the release branches are created from the **Master branch** for the upcoming release.

The branch cut happens on a **stipulated date decided according to the release calendar**.

The simplified process is:

```text id="r8m3q7"
Master Branch
      ↓
Branch Cut
      ↓
Release Branches
      ↓
SIT Build / Deployment
      ↓
SIT Validation
```

The purpose of branch cut is to create a controlled version of the code that will be used for the release.

After the release branches are created, the release process works with those release branches rather than continuously taking new changes from Master.

---

# 10. Why is Branch Cut Important?

Before branch cut, development changes can continue to be added to the Master branch.

After branch cut, the release needs to be stabilized.

The release branch represents the code scope intended for the upcoming release.

This gives the release team a stable version that can move through:

**SIT → QA → Sandbox → UAT → Pre-Prod → Production**

without continuously changing because of unrelated new development work.

---

# 11. What is SIT Cut?

**SIT cut** refers to the point in the release process where the release branch/build is prepared for SIT validation.

The release branch is used to build and deploy the release to SIT.

A simplified flow is:

```text id="j5x9v2"
Master
  ↓
Branch Cut
  ↓
Release Branch
  ↓
SIT Build
  ↓
SIT Deployment
  ↓
Smoke Tests
```

The exact pipeline names and implementation may vary by service, but the important concept is:

> **SIT cut establishes the release version that is going to be validated in SIT before the release moves to QA.**

---

# 12. What Happens After SIT Smoke Tests?

Once the required SIT smoke tests are green, the release can move toward QA.

The simplified flow is:

```text id="c7m2x9"
Release Branch
      ↓
SIT
      ↓
SIT Smoke Tests
      ↓
Smoke Tests Green
      ↓
QA
```

After the release is accepted in QA, the QA environment is **frozen**.

---

# 13. What is QA Freeze?

**QA freeze** means that the QA environment and release scope are stabilized.

Once QA is frozen:

> **No further new feature changes are normally accepted into the release.**

Only required **bug fixes** can be added through the approved **patch request** process.

The purpose of QA freeze is to prevent continuous changes from destabilizing the release while testing and validation are in progress.

The simplified model is:

```text id="p4n8y6"
QA Build Accepted
       ↓
QA Freeze
       ↓
No New Features
       ↓
Only Approved Bug Fixes
       ↓
Patch Request Process
```

---

# 14. Why is QA Frozen?

Suppose QA testing is already in progress.

If developers continue adding new features:

```text id="z2q7m5"
Feature A
Feature B
Feature C
Feature D
...
```

the QA environment keeps changing and it becomes difficult to know exactly what version was tested.

QA freeze provides stability.

After the freeze:

* New features are not normally added.
* Only necessary bug fixes are considered.
* Bug fixes require the appropriate patch request and approval.

This allows QA to continue testing a controlled release version.

---

# 15. What is a Patch Request?

A **patch request** is the controlled process used to deploy an approved bug fix after the QA environment has been frozen.

Once QA is frozen, a developer should not simply add another feature/change to the release.

If a bug needs to be fixed, the change goes through the patch request process.

The simplified flow is:

```text id="k6r3x8"
Bug Found in QA
      ↓
Bug Fix
      ↓
Patch Request
      ↓
QA Head / Lead Approval
      ↓
Build Release Branch
      ↓
Provide Override Version
      ↓
Deploy Approved Patch
```

---

# 16. Who Approves a Patch Request?

A patch request requires approval for deployment from the **QA Head/Lead**.

Therefore, creating the fix is not enough.

The patch must go through the appropriate approval before it is deployed into the frozen QA environment.

The key rule is:

> **After QA freeze, bug fixes are deployed through the patch request process and require QA Head/Lead approval.**

---

# 17. How is a Patch Request Raised?

The patch request is raised through the **respective QA pipeline of the service**.

When triggering the QA pipeline, provide the required **override version** in the parameter:

```text id="f8q2m6"
override_versions
```

The build used for the patch should be from the **Release branch**.

Therefore, the important requirements are:

```text id="w5n9r3"
QA Pipeline
    ↓
Release Branch Build
    ↓
override_versions = required patch/build version
    ↓
QA Deployment
```

---

# 18. What is `override_versions`?

`override_versions` is the parameter used to specify the **build/version that should be deployed instead of the default/recent version**.

For a QA patch deployment, the required patch version is provided through:

```text id="x3m7k9"
override_versions
```

This allows the QA deployment to use the specific build containing the approved bug fix.

---

# 19. Which Branch Should Be Used for a QA Patch?

The patch build should be from the **Release branch**.

This is important because QA is already frozen around the release version.

Therefore:

> **A QA patch should not be built from an arbitrary development branch. The documented patch process uses the Release branch.**

The simplified flow is:

```text id="b9k4q2"
Release Branch
      ↓
Patch Fix
      ↓
Build
      ↓
Specific Build Version
      ↓
override_versions
      ↓
QA Pipeline
      ↓
QA
```

---

# 20. Example — QA Bug Found After Freeze

Suppose a bug is found after QA has been frozen.

The correct approach is not:

```text id="u4m8p7"
Make random change
      ↓
Deploy directly to QA
```

Instead:

```text id="q6r2n9"
Bug Found
    ↓
Fix Bug
    ↓
Patch Request
    ↓
QA Head / Lead Approval
    ↓
Build Release Branch
    ↓
Use override_versions
    ↓
Trigger QA Pipeline
    ↓
Deploy Patch to QA
```

This keeps the QA release controlled.

---

# 21. What Changes Are Allowed After QA Freeze?

After QA freeze:

### Normally not allowed

* New feature development changes
* Unrelated enhancements
* Unnecessary changes to the release

### Allowed through the patch process

* Required bug fixes

The bug fix must follow the appropriate patch request and approval process.

The purpose is to keep the release stable while still allowing critical issues found during testing to be corrected.

---

# 22. Branch Cut → SIT → QA Freeze → Patch Process

These concepts are connected and should be understood as one release lifecycle.

```text id="e1x7m4"
                 RELEASE CALENDAR
                       ↓
                  Branch Cut
                       ↓
              Master → Release Branch
                       ↓
                    SIT Cut
                       ↓
                  SIT Deployment
                       ↓
                SIT Smoke Tests
                       ↓
                  Smoke Green
                       ↓
                       QA
                       ↓
                   QA Testing
                       ↓
                   QA Freeze
                       ↓
          ┌────────────┴─────────────┐
          │                          │
       No new                   Bug found
       features                      │
          │                          ↓
          │                    Patch Request
          │                          ↓
          │                  QA Head/Lead Approval
          │                          ↓
          │                  Release Branch Build
          │                          ↓
          │                    override_versions
          │                          ↓
          │                    QA Deployment
          │                          │
          └──────────────┬───────────┘
                         ↓
                     QA Accepted
                         ↓
                      Sandbox
                         ↓
                       UAT
                         ↓
                     Pre-Prod
                         ↓
                    Production
```

---

# 23. What Happens During QA?

QA performs more detailed validation.

The documented QA cycle includes:

* Functional testing
* Regression testing
* Backward compatibility checks

The target QA window is Day 1–4.

The goal is to make sure the release is stable enough to progress to Sandbox.

Once the QA build is accepted, QA is frozen.

---

# 24. What is Regression Testing?

Regression testing checks whether the new changes have accidentally broken existing functionality.

For example:

A developer changes the loan application flow.

Regression testing checks not only the new loan functionality but also existing functionality that could have been affected by the change.

Regression evidence is mandatory for production promotion.

---

# 25. What is Backward Compatibility?

Backward compatibility means checking that the new version continues to work correctly with existing components that depend on it.

For example:

**Service A → Service B**

If Service B is changed, we need to make sure Service A can still communicate with Service B correctly.

This is especially important for shared services and API contracts.

Backward compatibility evidence is required before production promotion.

---

# 26. What Happens During Sandbox Promotion?

After QA acceptance, the release is promoted to Sandbox.

The target is to push the release to Sandbox around Day 5.

The release is then validated for:

* Business/product journeys
* Contract compatibility
* Dependency behavior

QA pipelines are frozen after the QA build is accepted, and Sandbox is frozen after promotion.

---

# 27. What is UAT?

**UAT means User Acceptance Testing.**

It is the stage where the Product Owner/business stakeholders validate whether the release is acceptable from a business perspective.

The Product Owner sanity window has a target of 3 days.

Before the release can proceed, the release needs either:

* Explicit UAT sign-off, or
* Approved risk acceptance.

---

# 28. What is the Production Readiness Gate?

Before Production deployment, the release must be checked for operational readiness.

The following areas are verified:

* Vulnerabilities
* Configuration synchronization
* Secret synchronization
* Deployment sequence
* Rollback assets
* AOPM approval status

Think of this as the final:

> **"Are we actually ready to deploy?"**

check.

---

# 29. What Approvals Are Required Before Production?

The documented approvals include:

* QA sign-off
* IT approval
* Security/InfoSec approval
* Product Owner sign-off
* AOPM multi-level approval from L2–L5, where applicable

Production must be blocked if required approvals are incomplete.

---

# 30. What Can Block Production Go-Live?

Production go-live must be blocked if any required release gate is incomplete.

Examples include:

* Required approval is missing.
* Regression evidence is missing.
* Backward compatibility evidence is missing.
* High or critical security findings do not have approved risk treatment.
* Rollback readiness is missing.
* Required hotfix merge/reconciliation is incomplete.
* A common-service cut-off was missed without an approved extension.

This means:

**"The build is successful" does NOT automatically mean "the release can go to Production."**

A successful build is only one part of the overall release process.

---

# 31. What Evidence Is Required for a Product?

Every product included in the release needs evidence such as:

* Service version deployed
* UI version deployed
* Smoke test report
* Regression test report
* Backward compatibility status
* Known issues
* Waivers
* Mitigations
* Rollback notes
* Rollback owner
* QA sign-off
* UAT sign-off

The evidence provides proof that the product has passed the required release checks.

---

# 32. What is Rollback?

**Rollback** means returning a product or service to a previously known working version when the new release causes a serious problem.

Each product and shared service must have a rollback plan.

The rollback preparation should include:

1. A rollback artifact or tag.
2. Clearly defined rollback triggers.
3. A rollback executor.
4. A rollback approver.
5. The exact rollback pipeline or command.
6. Post-rollback validation steps.

---

# 33. When Should We Roll Back?

Examples of documented rollback triggers include:

* Critical journey failure
* Sustained error rate
* Data inconsistency
* Severe security issue

The exact rollback decision depends on the release governance and approved rollback criteria.

---

# 34. What is the Vulnerability Dashboard?

The Non-Prod Environment Vulnerability Dashboard is used to review security vulnerabilities for services.

The documented process is:

1. Open the vulnerability dashboard.
2. Select **Group by Service Name**.
3. Expand the required service.
4. Review the grace period and applicable CVEs.
5. Complete remediation before the grace period expires.

The referenced dashboard is named:

**Spring Boot Upgrade Dashboard**

---

# 35. How Do I Check Smoke Tests Before SIT Cut or QA Freeze?

The documented process is:

1. Log in to Jenkins.
2. Navigate to:

```text id="v7m3q9"
Maximus_916_NonProd → sit → Master Deploy / Master Release Smoke
```

3. Review the service pipelines.
4. Confirm that the required smoke tests completed successfully.

The purpose is to make sure the services have passed their smoke validation before proceeding with the release process.

---

# 36. What is a Smoke Test?

A **smoke test** is a basic validation that checks whether the deployed service or application is working at a fundamental level.

For example, a smoke test may verify that a service is deployed successfully and its important basic flows are working.

Smoke testing is different from full regression testing.

**Smoke test:** Basic health/critical-flow validation.

**Regression test:** More comprehensive testing to make sure existing functionality has not been broken.

---

# 37. What is AOPM Approval?

AOPM approval is one of the governance approvals considered during the Production Readiness Gate.

The documented approval requirement is:

**AOPM multi-level approval from L2–L5, where applicable.**

If AOPM approval is required but incomplete, the release should not proceed to Production.

---

# 38. What is a Shared Service?

A **shared service** is a service used by multiple products or journeys.

Examples mentioned in the release documentation include:

* Identity
* Authentication
* Backoffice
* Common services

Because a shared service can affect multiple products, changes to it require additional dependency and compatibility consideration.

---

# 39. Why Are Shared Services More Sensitive?

A product-specific service may primarily affect one product.

A shared service can affect multiple products.

For example:

```text id="g4n8p2"
Product A ──→ Shared Service
Product B ──→ Shared Service
Product C ──→ Shared Service
```

If the shared service changes in a backward-incompatible way, multiple products could be affected.

Therefore, shared-service changes require careful:

* Dependency validation
* API contract validation
* Backward compatibility checks
* Deployment sequencing
* Rollback planning

---

# 40. What is a Release Manager Responsible For?

The Release Manager / Master Release Owner is responsible for coordinating the overall release.

Responsibilities include:

* Release timeline
* Dependency management
* Go/no-go orchestration
* Governance decisions
* Final release communication

The Release Manager does not necessarily perform every technical deployment. Instead, they coordinate the release and make sure the required gates and decisions are completed.

---

# 41. What is the Tech Lead Responsible For?

The Tech Lead is responsible for technical release readiness.

This includes:

* Change readiness
* Version traceability
* Configuration changes
* Secret changes
* Deployment validation

The Tech Lead helps ensure that the technical components are ready for deployment.

---

# 42. What is QA Responsible For?

QA is responsible for providing testing and quality evidence.

This includes:

* Smoke testing
* Regression testing
* Backward compatibility validation
* Defect disposition
* QA sign-off

QA also controls the post-freeze patch process by providing the required approval for patch deployment through the QA Head/Lead.

---

# 43. What is the Product Owner Responsible For?

The Product Owner/business stakeholder is responsible for business validation during UAT.

They provide:

* UAT validation
* Product-wise acceptance
* Explicit UAT sign-off

If the release is accepted with a known risk, the appropriate risk acceptance must also be captured.

---

# 44. What is DevOps / SRE Responsible For?

DevOps, Build Owners, and SRE are responsible for operational execution and support.

This includes:

* Pipeline execution order
* Promotion flow
* Monitoring gates
* Deployment execution
* Rollback support

---

# 45. What is the Difference Between a Build and a Release?

A **build** is the process of creating and validating a deployable version of the software.

A **release** is the larger process of taking the required versions through environments, testing, approvals, evidence collection, and finally Production deployment.

Therefore:

**Build successful ≠ Release approved**

A successful build is required, but the release also needs testing, approvals, security validation, rollback readiness, and other release gates.

---

# 46. What is the Difference Between a Release Branch and Master?

### Master Branch

Master contains the ongoing main development state of the code.

### Release Branch

A Release branch is created from Master during the scheduled **branch cut** for a particular release.

The Release branch represents the controlled code scope for that release.

The simplified relationship is:

```text id="n8r4m2"
Master
  │
  │ Branch Cut
  ↓
Release Branch
  │
  ├──→ SIT
  ├──→ QA
  ├──→ Sandbox
  ├──→ UAT
  ├──→ Pre-Prod
  └──→ Production
```

After QA freeze, approved bug fixes can be added through the patch process using the Release branch.

---

# 47. What is the Simplest Way to Understand the Entire Release Process?

Think of the process like this:

### 1. Plan

What are we releasing?

↓

### 2. Release Calendar

When will the release activities happen?

↓

### 3. Branch Cut

Create the Release branches from Master on the scheduled date.

↓

### 4. SIT Cut

Prepare the Release branch/build for SIT.

↓

### 5. SIT

Check that the release works.

↓

### 6. SIT Smoke

Confirm the required smoke tests are green.

↓

### 7. QA

Perform functional, regression, and compatibility testing.

↓

### 8. QA Freeze

Stop accepting new feature changes into the release.

↓

### 9. Patch if Required

If a bug is found:

**Patch Request → QA Head/Lead Approval → Release Branch Build → `override_versions` → QA Pipeline**

↓

### 10. Sandbox

Validate journeys and dependencies.

↓

### 11. UAT

Get business/Product Owner acceptance.

↓

### 12. Pre-Prod

Perform final Production readiness checks.

↓

### 13. Production

Deploy during the approved release window.

↓

### 14. Validate & Close

Run post-deployment smoke tests, monitor the system, and publish the closure information.

---

# 48. Important Beginner Rules

When answering questions about the Maximus Master Release process, keep these rules in mind:

### Rule 1 — A successful build is not the same as Production approval.

A build can be successful while the release is still blocked because of missing testing, approvals, security closure, or rollback readiness.

### Rule 2 — A branch cut is scheduled.

Branch cut happens on the stipulated date decided according to the release calendar.

### Rule 3 — Release branches are cut from Master.

The release branch represents the controlled code scope for the upcoming release.

### Rule 4 — SIT comes before QA.

The release is validated in SIT and required smoke tests must be green before progressing through the release flow.

### Rule 5 — QA is frozen after the QA build is accepted.

After QA freeze, new feature changes are not normally accepted.

### Rule 6 — Bug fixes after QA freeze require a patch request.

Do not treat a post-freeze bug fix as a normal new feature deployment.

### Rule 7 — Patch requests require QA Head/Lead approval.

Approval is required before the patch is deployed into the frozen QA environment.

### Rule 8 — QA patches use the Release branch.

The patch build should be from the Release branch.

### Rule 9 — `override_versions` identifies the required patch version.

When triggering the respective QA pipeline for a patch, provide the required build/version through the `override_versions` parameter.

### Rule 10 — Required gates cannot simply be skipped.

Products cannot bypass shared quality, security, approval, or rollback requirements.

### Rule 11 — Missing evidence can block the release.

Regression, backward compatibility, smoke, approval, and other required evidence must be available as applicable.

### Rule 12 — Security issues can block Production.

Open high or critical security findings require approved risk treatment before Production.

### Rule 13 — Always consider rollback.

Every product and shared service should have a clear rollback method and owner.

### Rule 14 — Shared services require extra attention.

A shared service can affect multiple products, so compatibility and dependency validation are important.

---

# 49. Beginner Question → Simple Answer Patterns

### "What is a branch cut?"

Branch cut is the scheduled point when release branches are created from Master for the upcoming release. The date is decided according to the release calendar.

### "When does branch cut happen?"

It happens on the stipulated date defined by the release calendar.

### "Why do we create a release branch?"

It creates a controlled code scope for the release so that the release can move through SIT, QA, Sandbox, UAT, Pre-Prod, and Production without continuously picking up unrelated new development changes.

### "What is SIT cut?"

SIT cut refers to preparing the release branch/build for SIT validation.

### "What happens after SIT smoke tests are green?"

The release can proceed toward QA according to the release process.

### "What is QA freeze?"

QA freeze means the QA release scope is stabilized. New feature changes are no longer normally accepted; required bug fixes must go through the patch request process.

### "Can I deploy a new feature after QA freeze?"

No, new feature changes are not normally accepted after QA freeze.

### "What if I find a bug after QA freeze?"

Raise a patch request and follow the approved patch deployment process.

### "Who approves a QA patch?"

The QA Head/Lead provides the required approval for patch deployment.

### "Which branch should I use for a QA patch?"

The patch build should be from the **Release branch**.

### "Where do I provide the patch version?"

When triggering the respective QA pipeline, provide the required build/version in the:

```text id="c2v7m9"
override_versions
```

parameter.

### "Can I use the latest build for the patch?"

Do not assume that the latest build is the required patch. The patch process uses the specific required version through `override_versions`.

### "Does QA freeze mean no changes are possible?"

No. Required bug fixes can still be deployed through the approved patch request process.

### "If my build is successful, can I deploy to Production?"

Not necessarily.

A successful build only confirms that the build completed successfully. Production also requires the required testing evidence, approvals, security treatment, rollback readiness, and other release gates.

### "Can we directly deploy from SIT to Production?"

No. The standard Master Release flow is:

**SIT → QA → Sandbox → UAT → Pre-Prod → Production**

The release must pass the required gates and approvals before Production.

### "Why do we need UAT?"

UAT is used for business/Product Owner validation. The purpose is to confirm that the product is acceptable from a business perspective before Production.

### "What happens if regression testing is incomplete?"

Production promotion should be blocked until the required regression evidence is available or an explicitly approved exception/risk treatment exists.

### "What happens if there is a critical vulnerability?"

Production go-live should be blocked unless an approved risk treatment exists.

### "Why do we need rollback?"

If a Production deployment causes a critical problem, rollback provides a way to return to a known working version.

### "Who decides whether the release can go to Production?"

The release decision is part of the release governance process. The Release Manager coordinates the go/no-go process, while required QA, UAT, security, IT, Product Owner, and other applicable approvals must be completed.

---

# 50. Important Terminology

| Term                   | Simple meaning                                                        |
| ---------------------- | --------------------------------------------------------------------- |
| Master Release         | Planned release across multiple products/services                     |
| Hotfix                 | Urgent release for a critical bug, security, or compliance issue      |
| Release Train          | Group of products/services released together                          |
| Release Calendar       | Schedule defining planned release activities and dates                |
| Branch Cut             | Creation of release branches from Master on the scheduled date        |
| Release Branch         | Controlled branch containing the code scope for a release             |
| SIT Cut                | Preparation of the release branch/build for SIT validation            |
| SIT                    | Early integration/system validation environment                       |
| QA                     | Environment for functional, regression, and compatibility testing     |
| QA Freeze              | Point after which new feature changes are not normally accepted       |
| Patch Request          | Controlled process for deploying an approved bug fix after QA freeze  |
| `override_versions`    | Parameter used to specify the required build/version for deployment   |
| Sandbox                | Environment used for further release validation                       |
| UAT                    | Business/Product Owner acceptance testing                             |
| Pre-Prod               | Final environment before Production                                   |
| Production             | Live environment                                                      |
| Smoke Test             | Basic validation that the deployment/critical flows work              |
| Regression Test        | Testing existing functionality after changes                          |
| Backward Compatibility | Ensuring new changes continue to work with existing consumers         |
| Rollback               | Returning to a previous working version                               |
| Go/No-Go               | Decision on whether the release is ready to proceed                   |
| Cut-Off                | Deadline after which changes are restricted for the release           |
| Sign-Off               | Formal confirmation that a stakeholder approves the release           |
| Risk Acceptance        | Formal acceptance of a known release risk                             |
| AOPM                   | A required governance approval where applicable                       |
| CVE                    | Identifier for a known security vulnerability                         |
| SPOC                   | Single Point of Contact                                               |
| Release Evidence       | Proof that required testing, validation, and approvals were completed |

---

# 51. Source Boundaries

This context is based on the documented Maximus release process and the additional documented branch-cut, SIT-cut, QA-freeze, and patch-request process.

If a question asks for a specific Jenkins URL, deployment URL, command, configuration value, or product-specific procedure that is not documented here, do **not** invent the information.

Instead, clearly state that the available release documentation does not contain that specific detail.

For patch deployment specifically, this context establishes that:

* The patch is raised through the respective QA pipeline.
* The build should be from the Release branch.
* The required version is supplied through `override_versions`.
* QA Head/Lead approval is required.

If the user asks for additional patch-request steps that are not described here, state that those steps are not available in the current context rather than guessing.
