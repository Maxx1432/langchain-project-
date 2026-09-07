# SIT, SIT1, QA, QA1, PERF, and prodSandbox Deployment

## Overview

This document explains how to deploy a service to the following environments using Jenkins:

* SIT
* SIT1
* QA
* QA1
* PERF
* prodSandbox

The deployment is performed by running the appropriate **deployment pipeline** using Jenkins **Build With Parameters**.

The most important concept for a new joiner is:

> **A deployment pipeline takes a particular build/version and deploys it to a selected environment using the parameters provided to the pipeline.**

The high-level flow is:

```text
Select Deployment Pipeline
        ↓
Build With Parameters
        ↓
Provide deployment parameters
        ↓
Select the required build/version
        ↓
Apply Helm/configuration changes if required
        ↓
Run deployment
        ↓
Application deployed to environment
```

---

# 1. Deployment Pipelines

There is a separate deployment pipeline for each environment.

The naming convention is:

```text
Service Name-<environment>-deploy
```

The documented pipelines are:

| Environment | Deployment Pipeline               |
| ----------- | --------------------------------- |
| SIT         | `Service Name-sit-deploy`         |
| SIT1        | `Service Name-sit1-deploy`        |
| QA          | `Service Name-qa-deploy`          |
| QA1         | `Service Name-qa1-deploy`         |
| PERF        | `Service Name-perf-deploy`        |
| prodSandbox | `Service Name-prodsandbox-deploy` |

For example, for a service called `home-loan`, the SIT deployment pipeline would follow the pattern:

```text
home-loan-sit-deploy
```

The exact service name depends on the application being deployed.

---

# 2. How to Start a Deployment

To deploy a service, open the deployment pipeline for the required environment and select:

```text
BUILD WITH PARAMETERS
```

The deployment pipeline will then display parameters that control how the deployment should be performed.

The important parameters are:

* `upstream_environment`
* `branch`
* `enableRegressionTests`
* `override_versions`
* `runTwistlock`
* `config_change`

The parameters should be understood as inputs to the deployment process.

A simple mental model is:

```text
Deployment Pipeline
        +
Deployment Parameters
        ↓
Deployment Configuration
        ↓
Deployment
```

---

# 3. `upstream_environment`

## What does it mean?

`upstream_environment` identifies the **upstream environment/source** from which the build is taken.

In simple terms:

> It tells the deployment process which environment/source the build is coming from.

Think of it as identifying the source environment associated with the version being deployed.

### Why is it important?

The deployment process needs to know where the build/version is coming from before it can deploy it to the target environment.

### Beginner example

Suppose you are deploying to QA.

The deployment process needs to know the upstream environment/source associated with the build being deployed.

That information is provided through:

```text
upstream_environment
```

### Important distinction

`upstream_environment` and the deployment target are not necessarily the same concept.

For example:

```text
upstream_environment
        ↓
Source of the build/version

Target deployment environment
        ↓
Environment where the application will be deployed
```

The target environment is determined by the deployment pipeline you run, such as:

```text
Service Name-sit-deploy
Service Name-qa-deploy
Service Name-perf-deploy
```

---

# 4. `branch`

## What does it mean?

`branch` specifies the **Helm branch** whose changes should be used during deployment.

Helm configuration is used as part of the deployment process.

Therefore:

> The `branch` parameter tells the deployment pipeline which Helm branch to use for the deployment.

### Why is this important?

The application code/build is only one part of a deployment.

The deployment process also needs the appropriate deployment configuration.

The selected Helm branch determines which Helm changes/configuration are used.

A simplified model is:

```text
Application Build
       +
Helm Branch
       ↓
Deployment
```

### Important distinction

Do not confuse:

```text
Application Git branch
```

with:

```text
Helm branch
```

The `branch` parameter described here is specifically the **Helm branch** used during deployment.

---

# 5. `enableRegressionTests`

## What does it mean?

This parameter controls whether regression tests should run as part of the deployment process.

It accepts:

```text
true
false
```

### If `true`

```text
enableRegressionTests = true
```

Regression tests will run.

### If `false`

```text
enableRegressionTests = false
```

Regression tests will be skipped.

### Simple understanding

Think of it as a switch:

```text
enableRegressionTests
        ↓
   ┌────┴────┐
 true       false
   ↓           ↓
Run tests   Skip tests
```

### When a new joiner sees this parameter

The first thing to understand is what value has been selected.

For example:

```text
enableRegressionTests = true
```

means:

> Regression testing is enabled for this deployment.

---

# 6. `override_versions`

## What does it mean?

`override_versions` specifies the **build version or versions that should be used for deployment** instead of the default or recent build version.

This parameter is useful when you do not want to deploy the default/recent version.

### Simple example

Suppose the deployment pipeline would normally use the latest available build.

You can provide a specific build version through:

```text
override_versions
```

The deployment process then uses the specified version instead.

The basic idea is:

```text
Default version
      ↓
Normally selected

override_versions provided
      ↓
Use specified build/version
```

### Why is this important?

It allows a specific already-built version to be selected for deployment.

For example:

```text
Build #3340
```

may already exist, and you may want the deployment process to use that particular version rather than automatically selecting another version.

---

# 7. `runTwistlock`

## What does it mean?

`runTwistlock` controls whether the **Twistlock security scan** should run during the deployment process.

It accepts:

```text
true
false
```

### If `true`

```text
runTwistlock = true
```

The Twistlock security scan will run.

### If `false`

```text
runTwistlock = false
```

The Twistlock security scan will be skipped.

The simple mental model is:

```text
runTwistlock
      ↓
 ┌────┴────┐
true      false
 ↓          ↓
Scan      Skip scan
```

### Why is this parameter important?

It controls whether the deployment process performs the configured Twistlock security scan.

---

# 8. `config_change`

## What does it mean?

`config_change` controls whether **Helm configuration changes** should be picked up and applied during the deployment.

It accepts:

```text
true
false
```

### If `true`

```text
config_change = true
```

Helm configuration changes will be picked up/applied.

### If `false`

```text
config_change = false
```

Helm configuration changes will not be updated.

The simple mental model is:

```text
config_change
      ↓
 ┌────┴────┐
true      false
 ↓          ↓
Apply     Do not update
Helm      Helm config
changes
```

### Why is this different from `branch`?

These two parameters are related but control different things.

`branch` answers:

> **Which Helm branch should be used?**

`config_change` answers:

> **Should Helm configuration changes be picked up/applied?**

So:

```text
branch
  ↓
Which Helm source?

config_change
  ↓
Should Helm config changes be applied?
```

---

# 9. Understanding All Parameters Together

The parameters should not be understood as isolated fields.

Together, they provide the deployment pipeline with the information it needs.

A simplified model is:

```text
upstream_environment
        ↓
Where/source does the build come from?

branch
        ↓
Which Helm branch should be used?

override_versions
        ↓
Which build/version should be deployed?

config_change
        ↓
Should Helm configuration changes be applied?

enableRegressionTests
        ↓
Should regression tests run?

runTwistlock
        ↓
Should the Twistlock security scan run?

        ↓
Deployment
```

---

# 10. Example Deployment Configuration

Suppose you want to deploy a specific build to SIT.

You open:

```text
Service Name-sit-deploy
```

and select:

```text
BUILD WITH PARAMETERS
```

You then provide the required parameters.

Conceptually:

```text
Target Pipeline:
Service Name-sit-deploy

Parameters:
    upstream_environment = <source environment>
    branch = <Helm branch>
    override_versions = <specific build/version>
    enableRegressionTests = true
    runTwistlock = true
    config_change = true
```

The deployment process then uses those inputs to determine how the deployment should be performed.

The exact parameter values to use depend on the deployment requirement and the pipeline configuration.

---

# 11. Deploying to Different Environments

The deployment mechanism is similar across the supported environments.

The main difference is the **deployment pipeline that you select**.

### SIT

```text
Service Name-sit-deploy
```

### SIT1

```text
Service Name-sit1-deploy
```

### QA

```text
Service Name-qa-deploy
```

### QA1

```text
Service Name-qa1-deploy
```

### PERF

```text
Service Name-perf-deploy
```

### prodSandbox

```text
Service Name-prodsandbox-deploy
```

Therefore, if someone asks:

> "How do I deploy this service to QA?"

The first step is to use:

```text
Service Name-qa-deploy
```

and then select:

```text
BUILD WITH PARAMETERS
```

Similarly, for SIT:

```text
Service Name-sit-deploy
```

---

# 12. Deployment Pipeline vs Environment

A common beginner mistake is to think that the environment is selected only through a parameter.

In this documented process, the **pipeline itself identifies the target environment**.

For example:

```text
Service Name-sit-deploy
        ↓
SIT

Service Name-qa-deploy
        ↓
QA

Service Name-perf-deploy
        ↓
PERF
```

So the first question should be:

> **Which environment do I want to deploy to?**

Then select the corresponding deployment pipeline.

---

# 13. Build vs Deployment

It is important not to confuse the build pipeline with the deployment pipeline.

### Build pipeline

The build pipeline builds the application code.

```text
Code
 ↓
Build
 ↓
Build artifact / image
```

### Deployment pipeline

The deployment pipeline takes a build/version and deploys it to an environment.

```text
Build/version
      +
Deployment configuration
      ↓
SIT / SIT1 / QA / QA1 / PERF / prodSandbox
```

Therefore:

> **Build creates the deployable output; deployment uses a build/version and deploys it to an environment.**

---

# 14. Parameter Cheat Sheet

| Parameter               | Simple Meaning                                | `true`               | `false`               |
| ----------------------- | --------------------------------------------- | -------------------- | --------------------- |
| `upstream_environment`  | Source/upstream environment for the build     | —                    | —                     |
| `branch`                | Helm branch to use                            | —                    | —                     |
| `enableRegressionTests` | Whether regression tests should run           | Run regression tests | Skip regression tests |
| `override_versions`     | Specific build/version to deploy              | —                    | —                     |
| `runTwistlock`          | Whether Twistlock scan should run             | Run scan             | Skip scan             |
| `config_change`         | Whether Helm config changes should be applied | Apply changes        | Do not update changes |

---

# 15. Common New Joiner Questions

### How do I deploy a service to SIT?

Open the service's:

```text
Service Name-sit-deploy
```

pipeline and select **Build With Parameters**.

Then provide the required deployment parameters.

### How do I deploy to QA?

Use:

```text
Service Name-qa-deploy
```

and run **Build With Parameters**.

### What is the difference between SIT and SIT1?

They are separate deployment environments and therefore have separate deployment pipelines:

```text
SIT  → Service Name-sit-deploy
SIT1 → Service Name-sit1-deploy
```

### What is the difference between QA and QA1?

They are separate deployment environments with separate deployment pipelines:

```text
QA  → Service Name-qa-deploy
QA1 → Service Name-qa1-deploy
```

### What does `upstream_environment` mean?

It identifies the upstream environment/source from which the build is taken.

### What does `branch` mean in the deployment pipeline?

It means the **Helm branch** whose changes should be used for the deployment.

It should not automatically be interpreted as the application's Git branch.

### What does `override_versions` do?

It allows you to specify the build version(s) that should be deployed instead of the default/recent version.

### What happens if `enableRegressionTests` is true?

Regression tests will run.

### What happens if `enableRegressionTests` is false?

Regression tests will be skipped.

### What does `runTwistlock` control?

It controls whether the Twistlock security scan runs.

### What does `config_change = true` mean?

It means Helm configuration changes will be picked up/applied.

### What does `config_change = false` mean?

It means Helm configuration changes will not be updated.

### Does `branch` select the application code branch?

Not according to this deployment context.

Here, `branch` specifically means the **Helm branch** used for deployment.

### Can I deploy a specific build instead of the latest/default build?

Yes. The `override_versions` parameter is used to specify the build version(s) to use instead of the default/recent version.

### Is the deployment pipeline the same as the build pipeline?

No.

A **build pipeline** builds the application.

A **deployment pipeline** takes a build/version and deploys it to an environment.

---

# 16. Troubleshooting Deployment Parameters

When a deployment does not behave as expected, first check the parameters that were supplied.

A useful troubleshooting order is:

```text
1. Which deployment pipeline was run?
             ↓
2. Which environment does that pipeline target?
             ↓
3. What upstream_environment was selected?
             ↓
4. Which Helm branch was selected?
             ↓
5. Was override_versions provided?
             ↓
6. Were regression tests enabled?
             ↓
7. Was Twistlock enabled?
             ↓
8. Was config_change enabled?
```

This helps determine whether the deployment was executed with the expected inputs.

---

# 17. Beginner Mental Model

Remember the deployment process as:

```text
1. Decide TARGET environment
          ↓
2. Select corresponding deployment pipeline
          ↓
3. Click BUILD WITH PARAMETERS
          ↓
4. Provide deployment parameters
          ↓
5. Select required build/version
          ↓
6. Select Helm branch
          ↓
7. Decide whether tests/security/config changes are enabled
          ↓
8. Run deployment
```

The six most important parameters can be remembered as:

```text
SOURCE
  ↓
upstream_environment

HELM
  ↓
branch

VERSION
  ↓
override_versions

TESTING
  ↓
enableRegressionTests

SECURITY
  ↓
runTwistlock

CONFIG
  ↓
config_change
```

---

# Rules for Answering Deployment Questions

When answering a new joiner's question about SIT, SIT1, QA, QA1, PERF, or prodSandbox deployment:

* First identify the **target environment**.
* Tell the user which deployment pipeline corresponds to that environment.
* Explain that deployment is started using **Build With Parameters**.
* Explain parameters in simple terms before discussing their values.
* Clearly distinguish `upstream_environment` from the target deployment environment.
* Explain that `branch` refers to the **Helm branch** in this context.
* Explain `override_versions` as the way to specify the build/version to deploy instead of the default/recent version.
* Explain `enableRegressionTests`, `runTwistlock`, and `config_change` as switches controlling their respective behaviors.
* Do not assume that `true` or `false` is always the correct value for a parameter unless the required value is explicitly documented.
* Do not assume that SIT, SIT1, QA, QA1, PERF, and prodSandbox have identical deployment requirements beyond the documented pipeline/parameter behavior.
* Do not invent additional parameter meanings or deployment steps.
* If the question asks for the exact value to enter for a parameter and that value is not documented here, state that the exact value is not specified in this context rather than guessing.
