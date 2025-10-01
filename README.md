# Huawei CCE Kubeconfig Setup

A GitHub Action that generates a **kubeconfig** file for [Huawei Cloud CCE Autopilot](https://support.huaweicloud.com/intl/en-us/cce-autopilot/index.html) clusters, making it easy to interact with your cluster inside CI/CD pipelines.

This action installs the Huawei Cloud CLI, `kubectl`, and related dependencies, then generates and configures a kubeconfig for the target cluster.



---

## 📦 Inputs

| Name         | Description                           | Required | Default      |
| ------------ | ------------------------------------- | -------- | ------------ |
| `project_id` | Huawei Cloud Project ID               | ✅ Yes    | –            |
| `cluster_id` | Huawei Cloud CCE Cluster ID           | ✅ Yes    | –            |
| `region`     | Huawei Cloud region                   | ✅ Yes    | `af-south-1` |
| `duration`   | Certificate validity in days (1–1825) | ❌ No     | `1825`       |
| `access_key` | Huawei Cloud Access Key               | ✅ Yes    | –            |
| `secret_key` | Huawei Cloud Secret Key               | ✅ Yes    | –            |

---

## 📤 Outputs

| Name         | Description                      |
| ------------ | -------------------------------- |
| `kubeconfig` | Path to the generated kubeconfig |

---

## 🚀 Usage

```yaml
name: Deploy to Huawei CCE

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Huawei CCE Kubeconfig
        uses: your-org/huawei-cce-kubeconfig-setup@v1
        with:
          project_id: ${{ secrets.HUAWEI_PROJECT_ID }}
          cluster_id: ${{ secrets.HUAWEI_CLUSTER_ID }}
          region: af-south-1
          access_key: ${{ secrets.HUAWEI_ACCESS_KEY }}
          secret_key: ${{ secrets.HUAWEI_SECRET_KEY }}

      - name: Verify cluster access
        run: kubectl get nodes
```

---

## 🔑 Permissions

* Requires **Huawei Cloud IAM** credentials (`access_key` and `secret_key`) with permissions to:

  * Access CCE clusters
  * Generate cluster certificates

You should store these values securely in GitHub **Secrets**.

---

## ⚙️ How It Works

1. Installs Huawei Cloud CLI, `kubectl`, and helpers (`jq`, `yq`, `expect`).
2. Configures Huawei Cloud CLI with the provided credentials.
3. Generates a cluster certificate via `hcloud CCE CreateAutopilotKubernetesClusterCert`.
4. Extracts the kubeconfig and writes it to `~/.kube/config`.
5. Exposes the kubeconfig path as an output for downstream jobs.
6. Runs `kubectl cluster-info` to verify access.

---

## 📝 Example Workflows

* **Run a simple command:**

  ```yaml
  - name: Check cluster info
    run: kubectl cluster-info
  ```

* **Deploy manifests:**

  ```yaml
  - name: Apply Kubernetes manifests
    run: kubectl apply -f k8s/
  ```

---

## 👤 Author

Created by **Lemrex**

---

Do you want me to also add a **badges section** (version, license, marketplace) like official GitHub Actions usually have?
