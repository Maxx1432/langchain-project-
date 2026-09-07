---
id: jenkins-deployment-pipelines
title: Jenkins Deployment Pipelines
description: Jenkins build and deployment pipelines for master and other branches, SIT, SIT1, QA, QA1, PERF, prodSanbox, Sandbox, SandboxNew, sandbox1, UAT, PROD, and Seed deployment.
target_service: service-name
domain: Jenkins Deployment Pipelines
icon: Workflow
tags:
  - Jenkins
  - Build
  - Deployment
  - SIT
  - SIT1
  - QA
  - QA1
  - PERF
  - prodSanbox
  - Sandbox
  - SandboxNew
  - sandbox1
  - UAT
  - PROD
  - Seed
---

# Jenkins Deployment Pipelines

## 01. Build Master and Other Branches

- **Summary**: Build pipelines for UI and backend services, Build With Parameters, branch selection, and master and other branch behavior.
- **Context**:
  - lessons/01-build-master-and-other-branches/01-build-pipeline-overview.md
  - lessons/01-build-master-and-other-branches/02-build-with-parameters-and-branch-behavior.md
- **Knowledge Check**:
  - **Question**: Which Jenkins option is used for the build pipeline?
  - **Type**: multiple_choice
  - **Options**:
    - Rebuild
    - [x] BUILD WITH PARAMATERS
    - Open Blue Ocean
    - Previous Build
  - **Explanation**: BUILD WITH PARAMATERS is used for the build pipeline.

## 02. Build Verification and Build Failure Checks

- **Summary**: Last commit verification, image format, Kubernetes.container_image verification, build failures, and Jenkins build options.
- **Context**:
  - lessons/02-build-verification-and-failure-checks/01-build-verification.md
  - lessons/02-build-verification-and-failure-checks/02-build-failure-and-build-options.md
- **Knowledge Check**:
  - **Question**: What can be searched in the console output of the build number to confirm the build?
  - **Type**: multiple_choice
  - **Options**:
    - Build number
    - [x] Last commit first 5 value
    - Start time
    - Duration
  - **Explanation**: In the console output of the build number, the last commit first 5 value can be searched and matched.

## 03. Master Branch Build Flow

- **Summary**: Master build, version manifest, SIT deployment, SIT smoke test, and QA deployment.
- **Context**:
  - lessons/03-master-branch-build-flow/01-master-branch-build-flow.md
- **Knowledge Check**:
  - **Question**: After the smoke tests pass successfully, where is the build promoted?
  - **Type**: multiple_choice
  - **Options**:
    - SIT
    - SIT1
    - [x] QA
    - PERF
  - **Explanation**: Only after the smoke tests pass successfully, the build is promoted to the QA environment.

## 04. SIT, SIT1, QA, QA1, PERF, and prodSanbox Deployment

- **Summary**: Deployment pipelines, deployment parameters, and upstream values for SIT, SIT1, QA, QA1, PERF, and prodSanbox.
- **Context**:
  - lessons/04-environment-deployment/01-deployment-pipelines-and-parameters.md
  - lessons/04-environment-deployment/02-upstream-environment-values.md
- **Knowledge Check**:
  - **Question**: What does `upstream_environment` define?
  - **Type**: multiple_choice
  - **Options**:
    - Helm branch to use
    - [x] The environment/source from which the build is taken
    - Build version(s) to use
    - Twistlock security scan
  - **Explanation**: upstream_environment defines the environment/source from which the build is taken.

## 05. Sandbox, Sandbox1, UAT, and All Release Deployment

- **Summary**: Deployment parameters and upstream values for Sandbox, Sandbox1, and UAT, plus all-release pipeline behavior.
- **Context**:
  - lessons/05-sandbox-uat-and-all-release/01-sandbox-sandbox1-and-uat-deployment.md
  - lessons/05-sandbox-uat-and-all-release/02-all-release-deployment.md
- **Knowledge Check**:
  - **Question**: Which pipeline can also be used to deploy on UAT and is used for PROD as well?
  - **Type**: multiple_choice
  - **Options**:
    - Service-name-sit-deploy
    - Service-name-qa-deploy
    - [x] Service-name-all-release-pipeline
    - Maximus-seed-build
  - **Explanation**: UAT can also be deployed by the all release pipeline of that service, which is used for PROD as well.

## 06. Seed Deployment

- **Summary**: Seed content, seed build branches, and Seed deployment parameters for SIT, SIT1, QA, QA1, PERF, SANDBOX, SANDBOX1, UAT, and PROD.
- **Context**:
  - lessons/06-seed-deployment/01-seed-overview-and-build.md
  - lessons/06-seed-deployment/02-seed-environment-deployment.md
- **Knowledge Check**:
  - **Question**: What does Seed contain?
  - **Type**: multiple_choice
  - **Options**:
    - Only Jenkins build numbers
    - Only Helm branches
    - [x] Feature toggles, master data, product specific configuration, and template data
    - Only smoke tests
  - **Explanation**: Seed contains feature toggles, master data, product specific configuration, and template data.
