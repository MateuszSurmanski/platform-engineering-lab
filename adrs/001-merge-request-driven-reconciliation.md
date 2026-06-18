# ADR-001: Merge Request Driven Reconciliation

## Status

Approved

## Context

The reconciler has to make changes across multiple repositories, including:

* Terraform repositories
* GitOps repositories
* Application repositories
* Other platform-managed repositories

### Option 1: Direct changes

The reconciler pushes directly to target repositories using Gitlab API or GIT

#### Advantages

* Fast 
* Simple implementation

#### Disadvantages

* No review process
* Direct pushes to the default branch
* Reverting changes is more difficult
* No feedback from the destination repository pipeline before applying changes

### Option 2: Merge Request driven changes

The reconciler creates Merge Requests to the target repositories. Additionally set auto-merge when destination repository has strong validation processes and guardrails in place.

#### Advantages

* Review and audit processes
* Changes are easier to revert
* The reconciler can use pipeline results as an additional validation step before merging.
* Safer change management process.

#### Disadvantages

* Harder to implementation
* Additional dependency on repository pipelines

## Decision

The platform uses merge request driven changes for all repositories managed by th reconciler.

## Consequences

### Positive

* Easy integration with existing repositories and workflows
* Changes are visible and revertable
* Higher confidence that changes are correct when the destination pipeline completes successfully.

### Negative

* The overall process takes longer because the reconciler must wait for pipeline execution in each targer repository. 
