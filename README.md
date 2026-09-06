# KreateRevo Helm Charts

Public Helm chart repository for KreateRevo products.

## KrevoPilot Agent

```powershell
helm repo add kreate-revo https://kreaterevo.github.io/Krevopilot-agent-helm-charts
helm repo update

helm upgrade --install krevopilot-agent kreate-revo/krevopilot-agent `
  --namespace krevopilot-system `
  --create-namespace `
  --set platform.url="https://api.krevopilot.com" `
  --set cluster.id="your-cluster-id" `
  --set agent.key="your-generated-agent-key"
```

Generate the cluster key inside KrevoPilot before installing the agent.

## Security review documents

The public repository intentionally contains the packaged Helm chart and these review documents,
not the agent runtime source:

- [RBAC](docs/rbac.md)
- [Configuration](docs/configuration.md)
- [Network](docs/network.md)
- [Report schema](docs/schema.md)
- [Redaction](docs/redaction.md)
- [Security](docs/security.md)
