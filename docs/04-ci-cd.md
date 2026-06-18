# CI/CD

## Overview

The platform provides standardized CI/CD pipelines for all applications.

Application teams focus on developing software while the platform team maintains reusable pipeline components and delivery standards.

The objective is to ensure that every application follows the same build, test and deployment process.

## Design Principles

The CI/CD model follows several principles:

* pipelines should be reusable
* platform standards should be centrally managed
* application repositories should remain lightweight
* updates should be propagated in a controlled way
* teams should not duplicate CI/CD logic

## Pipeline Ownership Model

Application teams own:

* application source code
* tests
* application configuration

Platform teams own:

* CI/CD components
* delivery standards
* security checks
* build process templates

This separation allows platform improvements without requiring teams to redesign their pipelines.

## Pipeline Architecture

```text
Application Repository
          |
          v
.gitlab-ci.yml
          |
          v
Platform CI Component
          |
          v
Build
          |
          +--> Unit Tests
          |
          +--> Security Checks
          |
          +--> Container Build
          |
          +--> GitOps Update
```

## Standard Pipeline Flow

A typical application pipeline performs the following steps:

```text
Source Code Commit
        |
        v
Validate
        |
        v
Test
        |
        v
Build Container Image
        |
        v
Push Image
        |
        v
Update GitOps Repository
        |
        v
Merge Request
        |
        v
Argo CD Deployment
```

## Container Build

Applications are packaged as OCI-compatible container images.

The platform is responsible for maintaining the standard build approach.

Example:

```text
Application Source
        |
        v
Container Build
        |
        v
Container Registry
```

The exact implementation may use:

* Buildah
* Docker
* Podman
* Cloud-native builders

The application contract should not depend on a specific build engine.

## GitOps Deployment Integration

Application pipelines do not deploy directly to Kubernetes.

Instead, they update the GitOps repository.

Example:

```text
Application Pipeline
        |
        v
Update Image Tag
        |
        v
GitOps Repository
        |
        v
Merge Request
        |
        v
Argo CD Sync
        |
        v
Kubernetes
```

This keeps Kubernetes state fully managed through Git.

## CI/CD Components

Reusable platform components provide:

* build logic
* test execution
* security scanning
* image publishing
* GitOps integration

Example:

```yaml
include:
  - component: platform/python-app
    ref: v1.0.0
```

Applications consume components rather than copying entire pipelines.

## Component Versioning

Platform components are versioned.

Example:

```yaml
ci:
  component: platform/python-app
  component_ref: v1.0.0
```

Versioning allows:

* predictable upgrades
* rollback capability
* compatibility management

## Platform-Driven Upgrades

Over time, platform standards evolve.

Examples:

* new security checks
* new deployment patterns
* new scanning tools
* pipeline optimizations

The platform may automatically generate merge requests updating component versions across application repositories.

Example:

```text
Platform Component v1.0.0
                |
                v
Platform Component v1.1.0
                |
                v
Generated Upgrade Merge Requests
                |
                v
Application Repositories
```

This allows standardization without directly modifying application repositories.

## Why Not Direct Deployments?

Direct deployment from application pipelines introduces several challenges:

* reduced auditability
* deployment logic duplication
* inconsistent environments
* weaker GitOps guarantees

The platform therefore treats GitOps repositories as the deployment interface.

## Future Improvements

Potential future enhancements:

* dependency scanning
* SBOM generation
* container signing
* supply chain security
* progressive delivery
* deployment approvals
* automated rollback mechanisms
