# KreateRevo Helm Charts

Public Helm chart repository for KreateRevo products.

## KrevoPilot Agent

```powershell
helm repo add kreate-revo https://kreaterevo.github.io/helm-charts
helm repo update

helm upgrade --install krevopilot-agent kreate-revo/krevopilot-agent `
  --namespace krevopilot-system `
  --create-namespace `
  --set platform.url="https://api.krevopilot.com" `
  --set cluster.id="your-cluster-id" `
  --set agent.key="your-generated-agent-key"
```

Generate the cluster key inside KrevoPilot before installing the agent.
