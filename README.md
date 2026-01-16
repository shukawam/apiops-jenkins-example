# APIOps Jenkins Example

This repository is example of APIOps pipeline using Jenkins.

## How to use?

### Jenkins Credentials

This pipeline requires the following credentials to be configured in Jenkins:

| ID | Type | Description |
| :--- | :--- | :--- |
| `KONNECT_TOKEN` | Secret text | Your Kong Konnect Personal Access Token (PAT). Used by `deck` to sync configuration. |

### Pipeline Overview

The pipeline (`Jenkinsfile`) performs the following steps:
1.  **Checkout code**: Pulls the repository.
2.  **Setup deck**: Downloads and installs `deck` (v1.55.0).
3.  **Validate kong state**: Validates the `kong.yaml` file against the Konnect control plane.
4.  **Sync gateway settings**: Syncs the configuration to the Konnect control plane.

*Note: The Control Plane name is currently hardcoded as `local-gateway` in the `Jenkinsfile` environment variables.*
