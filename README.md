# platform-engineering-lab

This repository describes a reference architecture for a lightweight Internal Developer Platform built around GitLab, Terraform, GitOps, Argo CD and Kubernetes.

The goal of this project is to show how application onboarding, repository provisioning, CI/CD configuration and Kubernetes deployment can be standardized using a declarative contract and GitOps-based workflows.

This repository does not contain production source code. It focuses on architecture, patterns, contracts, design decisions and lessons learned.

## Table of Contents

- [Architecture](#architecture)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [High-Level Flow](#high-level-flow)
- [Core Components](#core-components)
- [Design Principles](#design-principles)
- [Documentation](#documentation)
- [Status](#status)

# Architecture

[![Platform Architecture](./docs/diagrams/big-picture.svg)](./docs/diagrams/big-picture.svg)

---

## Problem

Onboarding a new application to Kubernetes usually requires several manual steps:

* creating a Git repository
* configuring CI/CD
* provisioning GitLab resources
* creating Kubernetes namespace and deployment configuration
* registering the application in GitOps
* exposing the application through ingress
* optionally configuring external access through Cloudflare

When these steps are handled manually, teams often end up with inconsistent repositories, duplicated pipeline logic, unclear ownership and weak auditability.

## Proposed Solution

The proposed platform introduces an application contract as the main interface between developers and the platform.

A developer defines what should exist. The platform is responsible for reconciling this desired state into GitLab, Terraform, GitOps and Kubernetes configuration.

## High-Level Flow

```text
Developer
  |
  v
Application Contract
  |
  v
IDP Reconciler
  |
  +--> Terraform Repository
  |      |
  |      v
  |    GitLab resources
  |
  +--> Application Repository
  |      |
  |      v
  |    CI/CD pipeline
  |
  +--> GitOps Repository
  |      |
  |      v
  |    Argo CD
  |      |
  |      v
  |    Kubernetes
  |  
  +--> Cloudflare Repository
         |
         v
       Cloudflare Terraforming
         |
         v
       Ingress/Expose
```

## Core Components

| Component            | Responsibility                                                         |
| -------------------- | ---------------------------------------------------------------------- |
| Application Contract | Declarative input provided by a developer or platform user             |
| IDP Reconciler       | Reads the contract and creates merge requests to platform repositories |
| GitLab               | Source control, repository hosting and CI/CD execution                 |
| Terraform            | Provisioning GitLab resources and platform-managed configuration       |
| GitOps Repository    | Desired Kubernetes application state                                   |
| Argo CD              | Continuous reconciliation into Kubernetes                              |
| Kubernetes           | Runtime platform for applications                                      |
| Cloudflare           | Optional public or protected ingress layer                             |

## Design Principles

* Git is the source of truth.
* Platform changes are introduced through merge requests.
* Generated changes should be reviewable.
* Terraform owns infrastructure and GitLab resources.
* GitOps owns Kubernetes application state.
* CI/CD logic should be reusable and versioned.
* Application teams should describe intent, not implementation details.
* Ownership must be explicit from the beginning.

## Documentation

- [Overview](docs/01-overview.md)
- [Developer Onboarding](docs/02-developer-onboarding.md)
- [Repository Provisioning](docs/03-repository-provisioning.md)
- [CI/CD](docs/04-ci-cd.md)
- [GitOps Deployment](docs/05-gitops-deployment.md)
- [Architecture](docs/06-architecture.md)
- [Architecture Decision Records](docs/adrs/)

## Status

This is a work-in-progress architecture documentation project based on a homelab implementation of a small Internal Developer Platform.
