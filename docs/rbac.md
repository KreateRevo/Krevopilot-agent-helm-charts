# RBAC

This reference describes the permissions rendered by chart `0.1.45`. Always inspect the manifests
for the exact values you plan to install.

## Baseline read access

| API group | Resources | Verbs | Purpose |
|---|---|---|---|
| core | pods, events, nodes, namespaces, services | get, list | Workload health, topology and warning evidence |
| metrics.k8s.io | pods, nodes | get, list | CPU and memory observations |
| apps | replicasets | get, list | Resolve pod ownership |

## Conditional read access

`logs.enabled=true` adds `get` on `pods/log`.

`manifests.enabled=true` adds `get,list` for ConfigMaps, Secrets, Endpoints, PVCs, PVs,
Deployments, StatefulSets, DaemonSets, Jobs, CronJobs, HPAs, Ingresses, NetworkPolicies,
EndpointSlices, StorageClasses, CSIDrivers, admission webhooks and OpenShift Routes. Secret values
are excluded; see [Redaction](redaction.md).

`advancedMetrics.enabled=true` adds `get` on `nodes/proxy` for bounded kubelet summary telemetry.

The chart's collection role contains no `create`, `update`, `patch` or `delete` verbs.

```bash
helm template krevopilot-agent kreate-revo/krevopilot-agent \
  --version 0.1.45 \
  --namespace krevopilot-system \
  --set logs.enabled=false \
  --set manifests.enabled=true > rendered-agent.yaml
```

Review the rendered `ClusterRole` and `ClusterRoleBinding` before installation.
