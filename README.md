pipelines
=========

This repository is the single source of truth for all CI/CD pipeline logic across the **fullstack.pw** project. All GitHub Actions reusable workflows live here and are called from thin caller workflows in each application repository.

* * * * *

Reusable Workflows
------------------

All workflows are under `.github/workflows/` and use `workflow_call` as their only trigger.

### Build & Containerization

| Workflow | Description | Inputs |
|---|---|---|
| `build-and-push.yml` | Build a single Docker image via buildctl and push to Harbor | `app-context`, `app-name`, `app-dockerfile` (opt) |
| `build-changed-dockerfiles.yml` | Auto-discover changed Dockerfiles in a repo, derive image name from directory, build all | `registry` (opt), `library-prefix` (opt) |

### Deploy

| Workflow | Description | Inputs |
|---|---|---|
| `deploy-kustomize.yml` | Kustomize build + kubectl apply + rollout wait | `kustomize-dir`, `app-name`, `context` (opt) |
| `ephemeral-environment.yml` | Full PR ephemeral cluster lifecycle: Cluster API provisioning, Docker build, kustomize deploy, Cypress tests, cleanup | `image-name`, `dev-hostname-placeholder`, `deployment-name`, `cypress-spec`, `cypress-env-key`, `kustomize-overlay` (opt), `registry` (opt) |

### Infrastructure

| Workflow | Description | Inputs |
|---|---|---|
| `opentofu-infra.yml` | OpenTofu plan (on PR) and apply (on push to main) for the infra repo, including Cluster API wait, kubeconfig SOPS update, and Vault sync | none |
| `ansible.yml` | Ansible provisioning triggered by `[ansible <name>]` in commit message | `inventory-file` (opt), `playbook-dir` (opt), `vault-addr` (opt), `new-hosts-file` (opt) |

### Testing

| Workflow | Description | Inputs |
|---|---|---|
| `go-tests.yml` | `go test ./...` + golangci-lint | `go-dir` |
| `cypress.yml` | Cypress E2E runner | `start` (opt), `env-vars` (opt) |
| `iac-tests.yml` | Trivy IaC config scan with SARIF output | none |

### Security & Quality

| Workflow | Description | Inputs |
|---|---|---|
| `sec-trivy-fs.yml` | Trivy filesystem scan on the calling repo | none |
| `sec-trufflehog.yml` | TruffleHog secret scanning (`--results=verified,unknown`) | none |
| `conventional-commits.yml` | Validates PR title follows conventional commits spec | none |
| `release.yml` | Semantic release: changelog, git tag, GitHub release | none |

* * * * *

Callers
-------

| Repo | Workflows calling pipelines |
|---|---|
| `fullstack-pw/infra` | opentofu-infra, ansible, build-changed-dockerfiles, release, conventional-commits, sec-trivy-fs, sec-trufflehog |
| `fullstack-pw/cks-backend` | build-and-push, deploy-kustomize, ephemeral-environment |
| `fullstack-pw/cks-frontend` | build-and-push, deploy-kustomize, ephemeral-environment |

* * * * *

Runners
-------

| Label | Used by |
|---|---|
| `self-hosted` | Most workflows |
| `self-hosted-buildkit` | build-and-push, build-changed-dockerfiles |
