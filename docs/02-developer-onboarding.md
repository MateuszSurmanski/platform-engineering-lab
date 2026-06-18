# Developer Onboarding

Application onboarding starts with an application contract.

The contract describes the desired application state from the user's perspective.

## Input

Example contract:

```yaml
metadata:
  name: hello-app
  owner: team-platform

repository:
  namespace: owned/homelab/applications
  template: python-fastapi

runtime:
  port: 8000

ingress:
  host: hello-app.example.com
```

```text
Developer creates application contract
        |
        v
IDP reconciler validates contract
        |
        v
Reconciler creates merge requests
        |
        +--> GitLab repository provisioning
        +--> Application template repository
        +--> GitOps application configuration
        +--> Optional Cloudflare route
        |
        v
Merge requests are reviewed and merged
        |
        v
Application becomes available on Kubernetes
```