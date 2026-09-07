# Report schema

Regular reports are JSON documents sent over HTTPS. The top-level envelope contains a stable
cluster identifier, workload identifier, collection timestamp, redaction count and a `signals`
object.

`signals` may contain:

- agent and chart versions;
- collection-policy flags and summary counts;
- pod readiness, phase, restart and resource-request/limit evidence;
- Kubernetes warning events;
- node readiness and pressure signals;
- Metrics Server CPU and memory observations when available;
- bounded, redacted manifest summaries when enabled;
- supported policy, environment and platform-component observations.

Regular snapshots do not contain raw application logs. On-demand log evidence is a separate,
explicit request and is subject to configured limits and in-cluster redaction. The agent rejects a
regular report larger than its bounded payload limit.
