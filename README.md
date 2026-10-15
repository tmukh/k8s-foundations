# Kubernetes Foundations

A hands-on Kubernetes learning repository. The goal is to personally write,
run, break, diagnose and explain the core platform concepts before building
larger projects such as Sideout or homelab-agent.

This is a local learning lab, not a production platform. The first target is a
single-node Kubernetes cluster on the desktop PC. The cluster can be stopped
when it is not being used.

## Learning path

| Milestone | Focus | Status |
|---|---|---|
| M0 | Kubernetes objects and application lifecycle | Not started |
| M1 | Services, DNS and NetworkPolicies | Not started |
| M2 | Metrics, alerting and SLO foundations | Not started |
| M3 | Go cluster-inspection CLI | Not started |
| M4 | Optional read-only MCP wrapper | Not started |

The first four milestones are the core project. M4 is deliberately an
extension, not a prerequisite.

## Repository structure

```text
docs/
  learning-ledger.md  Concepts and personal understanding
  log.md              Predictions, break-it labs and results
  plan.md             Milestones and definition of done
labs/                 Disposable experiments
manifests/            Kubernetes resources kept after a lab is understood
src/                  Go CLI, introduced in M3
tests/                Automated tests
```

## Rules

- The core implementation is written by Tarik; assistance is limited to
  explanation, hints, review and small boilerplate.
- Every milestone includes one deliberate failure and a diagnosis.
- The cluster is disposable. Source, manifests and evidence belong in Git.
- No permanent observability stack, public endpoint, secrets or Argo CD
  dependency is needed for the first milestones.
- A skill moves to `Have` only after it has been personally implemented,
  tested and explained.

