# Oracle Cloud Ampere A1 Automated Provisioner

Automated GitHub Actions workflow to continuously attempt provisioning of Oracle Cloud Infrastructure (OCI) Always-Free Ampere A1 ARM compute instances in the Mumbai (`ap-mumbai-1`) region.

## Features
- **Frequency**: Runs automatically every 10 minutes via GitHub Actions cron schedule (`*/10 * * * *`) and on-demand via `workflow_dispatch`.
- **Pre-check**: Detects if an active or provisioning Ampere instance already exists in the tenancy; if so, exits immediately without doing redundant work.
- **Priority Tiering**:
  1. **Priority 1**: 4 OCPUs / 24 GB RAM / 100 GB Boot Volume (Full Always-Free allocation).
  2. **Priority 2 (Fallback)**: 2 OCPUs / 12 GB RAM / 100 GB Boot Volume (Half allocation if 4 OCPU is out of capacity).
- **Silent Failures (Zero Email Spam)**: Exits with status `0` when out of capacity, preventing GitHub Actions from spamming your email with failure alerts.
- **Instant Success Notification**: Automatically opens a GitHub Issue tagging the repository owner when an instance is provisioned, sending an immediate email notification with instance details.

## Required Secrets Configured
- `OCI_USER_OCID`
- `OCI_TENANCY_OCID`
- `OCI_FINGERPRINT`
- `OCI_REGION`
- `OCI_SUBNET_ID`
- `OCI_PRIVATE_KEY`
- `SSH_PUBLIC_KEY`
