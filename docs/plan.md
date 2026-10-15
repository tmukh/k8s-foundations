# Learning plan

## Skill-tree checklist

These are the skills this repository is intended to move from `Gap` or
`Exposure` to personally practised. Check them only when the evidence exists in
the milestone log and the concept has been explained back.

- [ ] Kubernetes core objects and lifecycle
- [ ] Kubernetes Services and service discovery
- [ ] Kubernetes scheduling and resource management
- [ ] Kubernetes probes and rollout behavior
- [ ] Kubernetes networking fundamentals
- [ ] NetworkPolicies and least-privilege traffic
- [ ] Prometheus metrics and PromQL
- [ ] Alert rules and failure diagnosis
- [ ] SLIs, SLOs, error budgets and burn rate
- [ ] Go CLI development
- [ ] Kubernetes Go client
- [ ] RBAC and service accounts
- [ ] Read-only MCP tools (optional M4)

## Definition of done for every milestone

1. Tarik writes the concept-carrying code or manifest.
2. The lab runs on the local desktop cluster.
3. A deliberate failure is predicted, observed and diagnosed.
4. Automated checks or repeatable commands preserve the result.
5. The relevant concepts are explained back and updated in the ledger.
6. `docs/log.md` records the evidence.

## M0 — Kubernetes objects and application lifecycle

### Build

- A Namespace.
- A Deployment for a small HTTP application.
- A Service with matching labels and selectors.
- Readiness and liveness probes.
- CPU and memory requests and limits.
- A rolling image/template update.
- A scale change.

### Technologies

- Kubernetes API objects
- `kubectl`
- YAML
- container images and registries
- Deployments
- ReplicaSets
- Pods
- Services
- Namespaces
- local single-node Kubernetes

### Concepts to learn

- Desired state versus observed state.
- Deployment -> ReplicaSet -> Pod ownership.
- Labels, selectors and EndpointSlices.
- Readiness versus liveness.
- Startup behavior and rollout availability.
- Resource requests versus limits.
- Scheduling and the causes of `Pending`.
- Scaling versus changing a Pod template.
- Rolling updates, `maxSurge` and `maxUnavailable`.
- `kubectl describe`, Events and `kubectl explain`.
- Image tags, digests and `imagePullPolicy`.

### Break-it lab

Make the Service selector wrong, predict the result, inspect the Endpoints or
EndpointSlice, and fix it. Then create an unschedulable Pod and diagnose its
resource request.

## M1 — Services, DNS and NetworkPolicies

### Build

- A server Deployment and internal Service.
- A client Pod that resolves and calls the Service.
- A NetworkPolicy that allows only the intended client.
- A second client that is denied.

### Technologies

- Kubernetes Service discovery
- CoreDNS
- NetworkPolicy
- Pod-to-Pod networking
- namespace and label selectors
- `kubectl exec`, logs and Events

### Concepts to learn

- ClusterIP Services and virtual IPs.
- Service DNS names: `<service>.<namespace>.svc`.
- DNS failure versus routing failure versus policy denial.
- NetworkPolicy ingress semantics.
- NetworkPolicy egress semantics.
- Default-deny policy design.
- Policy selectors and namespace labels.
- Why a NetworkPolicy needs a compatible CNI.
- Least privilege for east-west traffic.

### Break-it lab

Block the client deliberately. Distinguish a DNS problem from a policy
problem, identify the denied path, and restore only the required access.

## M2 — Metrics, alerting and SLO foundations

### Build

- A small application endpoint that exposes Prometheus metrics.
- A Prometheus instance used only for the lab.
- A request counter and latency histogram.
- PromQL queries for traffic, errors and latency.
- One alert rule.
- A simple availability and latency SLI.
- An SLO and its error budget.

### Technologies

- Prometheus
- PromQL
- Grafana concepts, without requiring a permanent Grafana deployment
- Alert rules
- histogram metrics
- Kubernetes probes and application metrics

### Concepts to learn

- Metrics versus logs versus traces.
- Counters, gauges and histograms.
- Labels and cardinality.
- Scraping and pull-based monitoring.
- Rates over counters.
- Histogram buckets and quantiles.
- Availability and latency SLIs.
- SLO targets and error budgets.
- Burn rate.
- Alert symptoms versus causes.
- Why a query can be syntactically valid but operationally useless.

### Break-it lab

Write an alert that cannot fire, generate traffic, inspect the time series,
explain why it stayed inactive, and correct the query.

## M3 — Go cluster-inspection CLI

### Build

- A Go CLI that lists namespaces and unhealthy Pods.
- Explicit error handling and useful exit codes.
- Human-readable output.
- Unit tests using a fake Kubernetes client.
- Deployment and Service inspection.
- Optional watch mode after the basic CLI is understood.

### Technologies

- Go
- Go modules
- Cobra or the standard `flag` package (choose deliberately)
- Kubernetes Go client
- unit testing and fake clients
- structured output

### Concepts to learn

- Go packages, modules and interfaces.
- Context cancellation and timeouts.
- Kubernetes API groups and typed clients.
- Authentication outside versus inside a cluster.
- Service-account credentials and kubeconfig.
- RBAC as least privilege.
- Client-side filtering versus API selectors.
- Exit codes and automation-friendly CLI behavior.
- Test doubles and fake API clients.
- Watch/list patterns and eventual consistency.

### Break-it lab

Give the CLI a missing or insufficient kubeconfig/RBAC permission. Predict the
failure, make the error actionable, and verify that the CLI does not silently
return an empty success result.

## M4 — Optional read-only MCP wrapper

This milestone comes only after M3. It is the bridge toward homelab-agent, not
part of the initial Kubernetes fundamentals requirement.

### Build

- An MCP server exposing a small allow-listed set of read-only inspection tools.
- No arbitrary command execution.
- No mutating Kubernetes operations.
- Tests for malformed arguments and denied operations.

### Technologies

- MCP server/client concepts
- structured tool schemas
- Go MCP SDK
- Kubernetes RBAC
- approval boundaries

### Concepts to learn

- Tools versus prompts versus resources.
- Schema validation at the boundary.
- Read-only capability design.
- Human approval for mutations.
- Tool errors versus successful empty results.
- Prompt injection as an agent boundary concern.
- Auditability and reproducible tool calls.
