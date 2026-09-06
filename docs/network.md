# Network

The agent initiates outbound connections; KrevoPilot does not need inbound network access to the
customer Kubernetes API.

- Allow HTTPS to the configured `platform.url`.
- Allow image pulls from the configured container registry.
- Allow DNS resolution.
- If an in-cluster Loki source is configured, allow the agent to reach that service.
- If diagnostics and the chart NetworkPolicy are enabled, list the required cluster Service CIDRs
  in `networkPolicy.diagnosticServiceCidrs`.

`networkPolicy.enabled=false` by default. When enabled, the chart can allow DNS and TCP/443 to the
CIDRs in `networkPolicy.egressCidrs`. Kubernetes NetworkPolicy cannot restrict traffic by hostname
or URL, and enforcement depends on the cluster CNI. Render and review the policy before applying it.
