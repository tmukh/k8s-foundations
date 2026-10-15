# 15/10/2026 - Setting up K3s

Setting up the namespace:

```YAML
apiVersion: v1
kind: **N**amespace # Capital N!!!
metadata:
    name: k8s-foundations # name of the namespace
    labels:
        name: k8s-foundations # under which "filter"
```

Then I ran `kubectl apply --dry-run=client -f manifests/namespace.yaml` to check that the YAML was valid without creating anything. Then I ran `kubectl apply -f manifests/namespace.yaml` to create the namespace.

Result: Namespace created and inspected via `kubectl describe namespace k8s-foundations`.

## Deployment and Pods

I then created a Deployment with three replicas. At first I thought that if I
did not add a container it would create the Pods but they would do nothing. In
reality, Kubernetes rejects that because a Pod needs at least one container to
run.

The Deployment created a ReplicaSet, and the ReplicaSet created three Pods:

```text
Deployment -> ReplicaSet -> Pods
```

The Pods were running nginx. I added a readiness probe and a liveness probe,
both checking `/` on port 80. The readiness probe checks whether the Pod should
receive traffic, while the liveness probe checks whether the container should
be restarted.

I also added resource requests and limits:

```yaml
requests:
  cpu: 250m
  memory: 64Mi
limits:
  cpu: 500m
  memory: 128Mi
```

The requests are what the scheduler uses when deciding whether a Pod can fit
on a node. The limits are the maximum resources the container is allowed to
use.

## Service

I created a Service with the selector `testLabel: test`, which matched the
labels on the Pods. The Service exposed port 80 and forwarded it to port 80 on
the Pods.

I curled the Service from inside the cluster and got the nginx response. This
showed that the Service selector found the Pods and that traffic could reach
them.

## Rolling update

I changed the nginx image from `nginx:1.14.2` to `nginx:1.27.4`. Kubernetes
created a new ReplicaSet because the Pod template changed. It gradually
created the new Pods and scaled down the old ReplicaSet.

The old ReplicaSets stayed there with zero replicas, so they could still be
used for rollback. The Deployment ended with three available Pods in the new
ReplicaSet.

## Scaling

I scaled the Deployment up and back down. Scaling changed the number of Pods
in the current ReplicaSet, but it did not create another ReplicaSet. This is
different from changing the Pod template:

```text
Template change -> new ReplicaSet
Replica count change -> same ReplicaSet, different number of Pods
```

## Scheduling failure

For the failure lab I created a separate Deployment with three Pods, but gave
each Pod a memory request of `64Gi`. The Pods stayed Pending because the node
could not satisfy the memory request.

`kubectl describe pod` showed:

```text
Node: <none>
PodScheduled: False
FailedScheduling: 1 Insufficient memory
```

This showed me that the scheduler looks at resource requests before assigning
a Pod to a node. The Deployment itself was accepted, but the Pods could not be
scheduled. I then deleted the separate failure-lab Deployment.

## My explanation

A Deployment describes the desired state for an application. It contains a Pod template and manages a ReplicaSet, which creates and maintains the requested number of Pods. The template defines labels, containers, images, resource requests and limits, and readiness/liveness probes. A Service provides a stable virtual IP and DNS name, selects matching Pods by labels, and routes traffic to their Pod IPs. A Namespace scopes resource names and groups related resources.
