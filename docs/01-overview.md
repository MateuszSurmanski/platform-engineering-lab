# Overview

This project describes a lightweight Internal Developer Platform pattern based on GitLab, Terraform, GitOps, Argo CD and Kubernetes.

The platform is designed around a declarative application contract. Instead of manually creating repositories, CI/CD configuration, GitOps manifests and ingress configuration, the user defines the desired application state in a YAML file. In te Enterprise solution YAML should be replaced of IDP Frontend Platform, for example backstage.io or another solution.
The IDP reconciler reads this contract and creates merge requests to the required platform repositories.

## Goals

The main goals are:

* standardize application onboarding
* reduce manual platform work for developers
* keep all changes auditable
* use Git as the source of truth
* separate application intent from platform implementation
* provide repeatable CI/CD and GitOps workflows

## Non-goals

This project does not try to replace existing CI/CD, GitOps or infrastructure tools.

It does not introduce a custom runtime platform. Instead, it connects existing tools into one consistent workflow.

The platform does not hide Kubernetes completely. It reduces the amount of Kubernetes knowledge required for basic application onboarding.

## Architecture Summary

The platform uses the following model:

```text
Application Contract
        |
        v
IDP Reconciler
        |
        +--> Terraform repository
        +--> Application repository
        +--> GitOps repository
        +--> Cloudflare configuration
```

The reconciler does not apply changes directly. It generates merge requests and sometimes sets automerge for non-critical repositories or non-critical path. This keeps platform changes reviewable, auditable and reversible.

## Main Idea

The application team defines what it needs.

The platform decides how to provision it.

Example:

```yaml
metadata:
  name: hello-app
  owner: team-platform

runtime:
  port: 8000
  replicas: 2

ingress:
  host: hello-app.example.com
```

The user does not need to know where Terraform resources are stored, how Argo CD applications are generated, or how CI/CD templates are wired internally.

## Key Design Choice

The platform follows a GitOps-first model.

This means:

* desired state is stored in Git
* changes are introduced through merge requests
* Argo CD reconciles Kubernetes state from Git
* Terraform provisions GitLab and platform resources from Git
