# Configuration

Chart `0.1.45` requires `platform.url`, `cluster.id` and either `agent.key` or
`agent.existingSecret`. Production installations should keep TLS verification enabled.

Important defaults:

| Setting | Default | Effect |
|---|---:|---|
| `agent.intervalSeconds` | `60` | Snapshot interval |
| `agent.maxPods` | `150` | Maximum pods per snapshot |
| `agent.maxEvents` | `60` | Maximum warning events per snapshot |
| `agent.verifyTls` | `true` | Verify the platform certificate |
| `agent.allowInsecureHttp` | `false` | Reject plain HTTP |
| `logs.enabled` | `false` | On-demand logs disabled |
| `diagnostics.enabled` | `false` | Active diagnostic checks disabled |
| `advancedMetrics.enabled` | `false` | Node-proxy metrics disabled |
| `manifests.enabled` | `true` | Redacted manifest summaries enabled |
| `manifests.secretMetadata` | `true` | Secret type and key names may be reported |
| `manifests.configMapValues` | `false` | ConfigMap values excluded |
| `networkPolicy.enabled` | `false` | No chart-managed egress policy |

Use `helm show values kreate-revo/krevopilot-agent --version 0.1.45` for the complete values file.
Do not commit an agent key, hash salt, log-source credential or rendered Kubernetes Secret.
