# Security

The published chart is designed for review before installation.

- The container runs as a fixed non-root user with a read-only root filesystem.
- Privilege escalation is disabled and all Linux capabilities are dropped.
- The normal collector uses read-focused RBAC documented in [RBAC](rbac.md).
- TLS verification is enabled and insecure HTTP is disabled by default.
- The connection key and hash salt are stored in a Kubernetes Secret or a supplied existing Secret.
- On-demand logs, diagnostic checks and node-proxy telemetry are opt-in.
- A chart-managed egress NetworkPolicy is opt-in and must be configured for the deployment network.

Before production use, pin an approved chart version and image digest, render the chart with the
intended values, review RBAC and egress, verify the image source, and rotate the agent key if it is
exposed. Report suspected vulnerabilities privately to KreateRevo rather than opening a public
issue with sensitive details.
