# Redaction

Redaction occurs in the agent before data leaves the cluster.

- Secret values are never transmitted. With manifest collection enabled, Secret existence, type
  and key names may be reported to diagnose missing references.
- Environment-variable values are excluded.
- ConfigMap values are excluded by default.
- Tokens, passwords, API keys, authorization values, cookies, credentials, private-key material
  and common connection strings are masked from collected text.
- On-demand logs always mask credential patterns. Email, public-IP and internal-IP masking are
  independently configurable; defaults mask email and public IP addresses.
- Pod, container and node identifiers use stable keyed aliases. Workload object names can be kept
  readable or aliased according to `manifests.realObjectNames`.
- Event text can be sent scrubbed, reduced to a reason/kind summary, or omitted.

Redaction is defense in depth, not permission to place credentials in labels, names, logs or
ConfigMaps. Review the selected values against your own data policy.
