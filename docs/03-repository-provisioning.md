# Repository Provisioning

## Overview

Repository provisioning is responsible for creating and managing application repositories in GitLab.

Instead of creating repositories manually through the GitLab user interface, repositories are declared and managed through Git.

The platform uses Terraform as the provisioning engine and GitLab merge requests as the change management mechanism.

## Motivation

Manual repository creation often leads to:

* inconsistent repository configuration
* missing CI/CD configuration
* missing ownership information
* different visibility settings
* lack of auditability

The platform standardizes repository creation through a declarative workflow.

## Desired State Model

The application contract defines the repository requirements.

Example:

```yaml
repository:
  namespace: owned/homelab/applications
  template: python-fastapi
  visibility: private
```

The contract does not create repositories directly.

Instead, it becomes the source for generating platform-managed Terraform configuration.

## Provisioning Flow

```text
Application Contract
        |
        v
IDP Reconciler
        |
        v
Terraform Repository
        |
        v
Merge Request
        |
        v
Terraform Apply
        |
        v
GitLab Repository
```

## Terraform Repository

The Terraform repository acts as the source of truth for GitLab resources.

Example structure:

```text
repositories/
├── applications
│   ├── hello-app.yaml
│   ├── payment-api.yaml
│   └── inventory-api.yaml
```

Each repository definition describes the desired GitLab project configuration.

Example:

```yaml
name: hello-app
description: Example application
namespace: owned/homelab/applications
visibility_level: private
```

Terraform converts these definitions into GitLab resources.

## Why Merge Requests?

The reconciler does not create repositories directly through the GitLab API.

Instead, it creates merge requests.

Benefits:

* auditable changes
* approval workflows
* rollback capability
* compliance-friendly process
* visibility into platform actions

Every repository change is represented by a Git commit.

## Repository Templates

A repository may be initialized from a platform template.

Examples:

* Python FastAPI service
* Go microservice
* Java Spring Boot service
* Infrastructure repository

Templates allow teams to start with a standardized structure.

Example:

```text
hello-app/
├── app/
├── tests/
├── Dockerfile
├── README.md
├── pyproject.toml
└── .gitlab-ci.yml
```

## Ownership

Ownership is defined at the application level.

Example:

```yaml
metadata:
  owner: team-platform
```

The owner is responsible for:

* application lifecycle
* repository maintenance
* merge request reviews
* platform onboarding decisions

Explicit ownership is a required platform concept.

## Design Decisions

### Git as Source of Truth

Repository definitions are stored in Git and reviewed through merge requests.

### Terraform Owns GitLab Resources

GitLab projects, groups and configuration are managed through Terraform.

### Generated Changes Remain Visible

The platform generates changes, but users can inspect them before they are applied.

### Platform Standards by Default

New repositories inherit predefined templates and CI/CD standards.

## Future Improvements

Potential future enhancements:

* template version management
* automated CI/CD component upgrades
