pipelines
=========

This repository contains the CI/CD pipelines that power the **fullstack.pw** project. These pipelines are designed to integrate seamlessly with various platforms (GitHub Actions, GitLab CI, etc.) and orchestrate tasks like building, testing, scanning, and deploying applications across multiple environments (dev, stg, prod).

* * * * *

Overview
--------

-   **Central Pipeline Definitions**: Houses all pipeline definitions (GitHub workflows, GitLab CI YAMLs) in one place.
-   **Multi-Environment Deployments**: Supports deployments to different Kubernetes clusters (dev, stg, prod) managed in the [infra](https://github.com/fullstack-pw/infra) repository.
-   **Self-Hosted Runners**: Utilizes self-hosted runners, including:
    -   GitHub Actions runners (actions-runner-controller) running on the Rancher Desktop cluster (Lenovo Legion).
    -   GitLab runners (gitlab-runner) deployed in the K3s clusters.

* * * * *

Key Features
------------

1.  **Build & Test**

    -   Automates code compilation, testing, and linting for projects like `api_usermgmt`.
    -   Runs security scans or static analysis, if configured.
2.  **Containerization & Registry**

    -   Builds Docker images and pushes them to the internal registry (`registry.fullstack.pw`) or another configured registry.
    -   Tags images by commit hash or semantic versioning, depending on the pipeline setup.
3.  **Deployment**

    -   Deploys container images to the desired Kubernetes environment (dev/stg/prod) via manifests, Helm charts, or Kustomize (as defined in [infra](https://github.com/fullstack-pw/infra)).
    -   Leverages GitOps or script-based deployments according to each environment's configuration.
4.  **Branch & Merge Strategies**

    -   Automatically triggers pipelines on pull/merge requests.
    -   Optionally includes approvals or manual gates (e.g., a manual promotion step between stg and prod).
5.  **Notifications & Reporting**

    -   Integration with Slack, email, or GitHub/GitLab status checks to provide feedback on pipeline runs.
    -   Summaries of test results or deployment statuses.