# Yamlr GitHub Action

Official GitHub Action for [Yamlr](https://yamlr.dev) — the autonomous Kubernetes manifest resolution and auto-healing engine.

Runs high-speed, offline schema validation and safe comment-preserving auto-repairs directly inside your GitHub Actions workflows.

---

## Usage

### 1. Read-Only Schema Gate (Audit Pull Requests)

Audit all Kubernetes manifests against official Kubernetes OpenAPI schemas and block PRs with structural errors:

```yaml
name: Kubernetes Manifest Gate
on: [push, pull_request]

jobs:
  yamlr-audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Audit Manifests
        uses: yamlr/action@v1
        with:
          path: './manifests'
          command: 'scan'
          fail-on: 'error'
```

---

### 2. Export SARIF to GitHub Code Scanning

Generate a native SARIF report and upload security and schema findings to GitHub's **Security $\rightarrow$ Code scanning alerts** tab:

```yaml
name: Kubernetes Security Scan
on: [push, pull_request]

jobs:
  yamlr-sarif:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Yamlr Scanner
        uses: yamlr/action@v1
        with:
          path: './k8s'
          command: 'scan'
          output: 'sarif'
        continue-on-error: true

      - name: Upload to GitHub Code Scanning
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: results.sarif
```

---

### 3. Auto-Heal Manifests in CI/CD

Automatically fix broken selectors, typo fields, ports, and schema deviations:

```yaml
name: Auto-Heal Kubernetes Manifests
on: [push]

jobs:
  yamlr-heal:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Apply Safe Auto-Repairs
        uses: yamlr/action@v1
        with:
          path: './manifests'
          command: 'heal'
          apply: 'true'
```

---

## Action Inputs

| Input | Description | Required | Default |
| :--- | :--- | :---: | :--- |
| `path` | Path to Kubernetes manifests directory or file. | No | `.` |
| `command` | Execution command: `scan`, `heal`, `fmt`, or `diff`. | No | `scan` |
| `apply` | Apply repairs in-place (when `command: heal`). | No | `false` |
| `fail-on` | Severity threshold to fail the job (`error`, `warning`). | No | `error` |
| `output` | Output format (`human`, `sarif`, `json`). | No | `human` |
| `k8s-version` | Target Kubernetes version (e.g. `1.30`). | No | *auto-detect* |
| `version` | Exact Yamlr release tag to install. | No | `v0.1.0` |

---

## License

Yamlr is proprietary software owned by Timonel Labs. See [yamlr.dev](https://yamlr.dev) for documentation and evaluation terms.
