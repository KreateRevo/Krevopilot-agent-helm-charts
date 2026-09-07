# KrevoPilot Agent — Helm chart

Read-only Kubernetes agent for [KrevoPilot](https://krevopilot.com). It collects workload health, warning events, node signals and Metrics Server usage, and sends them outbound over HTTPS so the platform can investigate incidents with evidence and recommend CPU and memory rightsizing.

The agent holds **read-only Kubernetes permissions**. It cannot create, patch or delete any object in your cluster.

---

## Install

```bash
helm repo add kreate-revo https://kreaterevo.github.io/Krevopilot-agent-helm-charts
helm repo update

helm upgrade --install krevopilot-agent kreate-revo/krevopilot-agent \
  --namespace krevopilot-system \
  --create-namespace \
  --set platform.url="https://api.krevopilot.com" \
  --set cluster.id="<your-cluster-id>" \
  --set agent.key="<your-generated-agent-key>"
```

<details>
<summary>PowerShell</summary>

```powershell
helm upgrade --install krevopilot-agent kreate-revo/krevopilot-agent `
  --namespace krevopilot-system `
  --create-namespace `
  --set platform.url="https://api.krevopilot.com" `
  --set cluster.id="<your-cluster-id>" `
  --set agent.key="<your-generated-agent-key>"
```

</details>

Generate `cluster.id` and `agent.key` in KrevoPilot before installing — the agent will not report without them. Full walkthrough: [Getting started](https://krevopilot.com/docs-getting-started.html).

To keep the key off the command line, create the Secret yourself and reference it with `agent.existingSecret`.

---

## What the agent sends

| Collected | Never collected |
|---|---|
| Pod, workload and node health | Kubernetes Secret values |
| Warning events (reason, kind, scrubbed message) | Environment variable values |
| Metrics Server CPU and memory usage | Application logs during routine snapshots |
| Resource requests and limits | Anything requiring write permissions |

Logs are **off by default**. When enabled, they are fetched only for a pod, container and time window a user explicitly requests, and are sanitized inside your cluster before they leave it.

Object names ship in the clear by default so engineers can act on them directly. Set `manifests.realObjectNames=false` to send HMAC-SHA256 aliases instead.

Details: [Security and data handling](https://krevopilot.com/docs-security.html) · [Agent and RBAC reference](https://krevopilot.com/agent-rbac.html)

---

## Common configuration

| Value | Default | Purpose |
|---|---|---|
| `platform.url` | `""` | KrevoPilot API endpoint. Required. |
| `cluster.id` | `""` | Cluster identifier from the platform. Required. |
| `agent.key` | `""` | Agent key. Required unless `agent.existingSecret` is set. |
| `agent.existingSecret` | `""` | Read the key from a Secret you manage. |
| `agent.intervalSeconds` | `60` | Snapshot interval. |
| `agent.maxPods` | `150` | Cap on pods per snapshot. |
| `agent.eventMessageMode` | `scrubbed` | `scrubbed`, `summary` or `none`. |
| `manifests.realObjectNames` | `true` | `false` sends privacy aliases instead of names. |
| `logs.enabled` | `false` | On-demand log retrieval. |
| `externalLogSource.type` | `none` | `loki`, `opensearch`, `splunk`, `datadog` or `kubernetes`. |
| `advancedMetrics.enabled` | `false` | CPU throttling, network and PV telemetry. |
| `networkPolicy.enabled` | `false` | Restrict agent egress to named CIDRs. |
| `rbac.create` | `true` | Create the read-only ClusterRole and binding. |

Every value: `helm show values kreate-revo/krevopilot-agent`

---

## Review the RBAC before installing

Render the chart with your intended flags and read the permissions it would create — nothing is applied:

```bash
helm template krevopilot-agent kreate-revo/krevopilot-agent \
  --set cluster.id=review \
  --set manifests.enabled=true \
  | grep -A40 "kind: ClusterRole"
```

The verbs are limited to `get`, `list` and `watch`. There is no `create`, `update`, `patch` or `delete` anywhere in the chart.

---

## Security review documents

This repository intentionally publishes the packaged Helm chart and the review
documents below, not the agent runtime source:

- [RBAC](docs/rbac.md) - every permission the chart grants, and why
- [Configuration](docs/configuration.md) - full value reference
- [Network](docs/network.md) - egress destinations and NetworkPolicy
- [Report schema](docs/schema.md) - exactly what a snapshot contains
- [Redaction](docs/redaction.md) - what is scrubbed, and where
- [Security](docs/security.md) - threat model and hardening

---

## Hardening defaults

The pod runs as non-root (UID 10001) with a read-only root filesystem, `allowPrivilegeEscalation: false`, and a 16Mi writable `emptyDir` for temporary files. Connections are outbound only — nothing needs to reach into your cluster, and no inbound port is exposed.

Set `networkPolicy.enabled=true` with `networkPolicy.egressCidrs` to restrict where the agent may connect.

---

## Upgrade and uninstall

```bash
helm repo update
helm upgrade krevopilot-agent kreate-revo/krevopilot-agent -n krevopilot-system

helm uninstall krevopilot-agent -n krevopilot-system
```

Uninstalling removes the agent, its ServiceAccount and its RBAC. Data already reported to the platform is managed from KrevoPilot.

---

## Troubleshooting

| Symptom | Usual cause |
|---|---|
| Agent reports 401 | Key mismatch, or a rotated key not yet applied |
| No data after install | `cluster.id` or `platform.url` unset, or egress blocked |
| Metrics unavailable | Metrics Server not installed in the cluster |
| Helm Secret conflict on upgrade | A previous release in a different namespace |

Full guide: [Troubleshooting](https://krevopilot.com/docs-troubleshooting.html)

Kubernetes workload failures, as opposed to agent problems, are covered separately:
[CrashLoopBackOff](https://krevopilot.com/fix-crashloopbackoff-kubernetes.html) ·
[OOMKilled and exit code 137](https://krevopilot.com/fix-oomkilled-exit-code-137.html) ·
[ImagePullBackOff](https://krevopilot.com/fix-imagepullbackoff-kubernetes.html) ·
[Resource rightsizing](https://krevopilot.com/kubernetes-resource-rightsizing.html)

---

## Support

Chart issues: open an issue on this repository.
Product questions and trials: [krevopilot.com](https://krevopilot.com) · support@krevopilot.com

KrevoPilot is owned and operated by KreateRevo UG, a Berlin-based German software company.
Charts are published under a proprietary licence; see [About](https://krevopilot.com/about.html).
