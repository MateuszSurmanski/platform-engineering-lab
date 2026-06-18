# GitOps Deployment

## Overview

The platform uses GitOps as the deployment model for Kubernetes workloads.

Application pipelines do not deploy directly to Kubernetes. Instead, they modify the desired state stored in a Git repository.

Argo CD continuously reconciles the desired state into the Kubernetes cluster.

## Motivation

Traditional deployment pipelines typically interact directly with Kubernetes:

```text
Developer
    |
    v
CI Pipeline
    |
    v
kubectl apply
    |
    v
Kubernetes
```

While simple, this model introduces several challenges:

* deployment logic is distributed across repositories
* Kubernetes state is not fully represented in Git
* rollback procedures vary between teams
* cluster changes are harder to audit
* deployment permissions must be granted to pipelines

The platform adopts GitOps to address these concerns.

## GitOps Model

The desired application state is stored in a dedicated GitOps repository.

Example:

```text
Application Repository
         |
         v
CI Pipeline
         |
         v
GitOps Repository
         |
         v
Argo CD
         |
         v
Kubernetes
```

Git becomes the source of truth for deployment state.

## Separation of Responsibilities

The platform separates application development from deployment management.

### Application Repository

Responsible for:

* source code
* tests
* Dockerfile
* application-specific logic

### GitOps Repository

Responsible for:

* deployment manifests
* Helm values
* image versions
* runtime configuration

### Argo CD

Responsible for:

* continuous reconciliation
* drift detection
* synchronization
* rollback through Git history

## Deployment Workflow

A deployment follows this sequence:

```text
Developer Push
        |
        v
CI Pipeline
        |
        v
Build Container Image
        |
        v
Push Image
        |
        v
Update GitOps Configuration
        |
        v
Create Merge Request
        |
        v
Merge
        |
        v
Argo CD Sync
        |
        v
Kubernetes Deployment
```

No direct Kubernetes access is required from the application pipeline.

## Example

Application image:

```text
registry.example.com/hello-app:e916e997
```

GitOps update:

```yaml
image:
  repository: registry.example.com/hello-app
  tag: e916e997
```

After the change is merged, Argo CD detects the modification and applies the new desired state.

## Desired State Reconciliation

Argo CD continuously compares:

```text
Git Desired State
        vs
Cluster Actual State
```

When differences are detected:

```text
Git
 |
 v
Desired State
 |
 v
Argo CD
 |
 v
Reconcile
 |
 v
Cluster
```

The cluster is automatically brought back to the declared state.

## Drift Detection

Manual changes in Kubernetes are considered configuration drift.

Example:

```text
kubectl edit deployment hello-app
```

Such modifications are temporary.

Argo CD identifies the drift and restores the configuration from Git.

Benefits:

* predictable environments
* reduced configuration drift
* stronger operational discipline

## Rollback Strategy

Rollback is implemented through Git.

Example:

```text
Commit A -> Version 1.0
Commit B -> Version 1.1
Commit C -> Version 1.2
```

To rollback:

```text
Revert Commit C
```

Argo CD reconciles the reverted state automatically.

No imperative deployment commands are required.

## Why Argo CD?

The platform uses Argo CD because it provides:

* Git-native workflows
* declarative deployments
* automated reconciliation
* drift detection
* deployment visibility
* multi-application management

Argo CD acts as the deployment controller rather than the deployment executor.

## Design Decisions

### Git Owns Deployment State

The deployment state must be observable and auditable.

### CI/CD Does Not Own Kubernetes

Pipelines produce artifacts and update desired state.

They do not interact directly with the cluster.

### Reconciliation Over Execution

The platform prefers continuous reconciliation over one-time deployment execution.

### Rollback Through Git

Git history is the deployment history.

## Future Improvements

Potential future enhancements:

* progressive delivery
* canary deployments
* blue/green deployments
* automated rollback triggers
* deployment promotion across environments
* policy-based deployment validation
