# Oracle Cloud Ampere A1 Automated Quota Maximizer

Automated, continuous GitHub Actions workflow to provision Oracle Cloud Infrastructure (OCI) Always-Free Ampere A1 (ARM) compute instances in the Mumbai (`ap-mumbai-1`) region until your entire free-tier quota (4 OCPUs, 24 GB RAM, 200 GB Storage) is fully claimed.

---

## 🚀 Key Architecture & Features

### 1. Continuous 5-Hour 15-Minute Self-Dispatching Relay
- **Zero Gap 24/7 Execution**: Runs a continuous loop checking Oracle capacity every **~35 seconds** without stopping.
- **Auto-Relay**: At the 5h 15m mark (safely before GitHub's 6-hour execution limit), the runner automatically dispatches the next relay runner (`gh workflow run`) and terminates with code `0`.
- **Backup Schedule**: Configured with a fallback cron (`0 */6 * * *`) every 6 hours as a safety net.

### 2. Option A: Dynamic Multi-Instance Quota Maximizer
The workflow does not prematurely stop if a smaller instance is secured. On each cycle, it queries your tenancy's active compute:
- **Remaining OCPUs**: `4 - (current active OCPUs)`
- **Remaining RAM**: `24 GB - (current active RAM)`

#### Hierarchy per Instance:
1. **Contender 1**: 4 OCPUs / 24 GB RAM / 100 GB Boot Disk (if full quota is available).
2. **Contender 2**: 2 OCPUs / 12 GB RAM / 100 GB Boot Disk.
3. **Contender 3**: 1 OCPU / 6 GB RAM / 100 GB Boot Disk.

*If VM #1 is claimed at 2 OCPUs / 12 GB RAM, the runner instantly continues hunting for VM #2 (2 OCPUs / 12 GB RAM) until all 4 OCPUs and 200 GB storage are claimed!*

### 3. Immediate Email Notifications (Zero Delay)
- The instant any instance moves to `"lifecycle-state": "PROVISIONING"`, the runner immediately opens a GitHub Issue with `--assignee` targeting the repository owner.
- GitHub dispatches an instant high-priority email alert to your registered inbox containing the instance ID, specifications, and instructions to connect.
- The runner continues hunting for remaining quota without terminating.

### 4. Zero Failure Email Spam
- Out-of-capacity runs exit with status `0` (Success). GitHub Actions treats normal capacity checks as successful, eliminating failure email spam.

---

## 🔐 Configured Secrets
All credentials are encrypted and stored in **GitHub Secrets**:
- `OCI_USER_OCID`
- `OCI_TENANCY_OCID`
- `OCI_FINGERPRINT`
- `OCI_REGION`
- `OCI_SUBNET_ID`
- `OCI_PRIVATE_KEY`
- `SSH_PUBLIC_KEY`
