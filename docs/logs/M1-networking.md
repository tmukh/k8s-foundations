# 16/10/2026 - Service DNS and NetworkPolicies

## Service DNS

I tested the existing Service from a temporary Pod in the same namespace.

The short name worked:

```text
k8s-foundation-service
```

The full Service DNS name worked too:

```text
k8s-foundation-service.k8s-foundations.svc
```

Both returned the same nginx page with a `200` response.

The short name works because the client Pod is in the same namespace as the
Service. The longer name includes the Service name and namespace, so it can be
used from a different namespace:

```text
<service>.<namespace>.svc
```

This is a DNS search-scope difference, not a different cluster or a
disconnected network.

## NetworkPolicy baseline

I created a separate M1 server Deployment and Service:

```text
m1-client -> m1-server-service -> m1-server Pod
```

Before adding a NetworkPolicy, the client could curl the server and received
the nginx response.

The Service selected the server using:

```yaml
app: m1-server
```

## Default deny

I created a default-deny ingress policy with an empty `podSelector`, which
selects every Pod in the namespace. It had no ingress rules.

After applying it, the client could still resolve the Service name, but the
HTTP request was denied. This showed that the policy applies to traffic
reaching the selected Pods, not to the Service object itself.

## Allow rule

I created an allow policy selecting the server Pod:

```yaml
app: m1-server
```

It allows only a client with this label to connect on TCP port 80:

```yaml
app: m1-client
```

The labeled client could curl the server successfully again. The two policies
work together:

```text
default-deny ingress
+ allow m1-client -> m1-server:80
= only the matching client is allowed
```

The important point is that NetworkPolicies are additive. The allow policy
does not remove the default deny; it adds the specific permitted traffic.

## Explain-back

A Service provides a stable virtual IP and DNS name and selects Pods by
labels. A short Service name resolves within the client's namespace, while a
namespace-qualified name can identify a Service in another namespace.

A NetworkPolicy selects the Pods it protects. A default-deny ingress policy
blocks incoming traffic to those Pods, and another policy can add a narrow
exception. The selector in the allow policy selects the server, while the
`from` selector selects the client that is allowed to connect.

