---
type: docs
title: "Rate limiting"
linkTitle: "Rate limiting"
description: "Use rate limit middleware to limit requests per second"
aliases:
- /developing-applications/middleware/supported-middleware/middleware-rate-limit/
---

The rate limit [HTTP middleware]({{< ref middleware.md >}}) allows restricting the maximum number of allowed HTTP requests per second. Rate limiting can protect your application from Denial of Service (DoS) attacks. DoS attacks can be initiated by malicious 3rd parties but also by bugs in your software (a.k.a. a "friendly fire" DoS attack).

## Component format

In the following definition, the maximum requests per second are set to 10:

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: ratelimit
spec:
  type: middleware.http.ratelimit
  version: v1
  metadata:
  - name: maxRequestsPerSecond
    value: 10
```

## Spec metadata fields

| Field | Details | Example |
|-------|---------|---------|
| `maxRequestsPerSecond` | The maximum requests per second by remote IP.<br>The component looks at the `X-Forwarded-For` and `X-Real-IP` headers to determine the caller's IP. | `10`

Once the limit is reached, the requests will fail with HTTP Status code *429: Too Many Requests*.

{{% alert title="Important" color="warning" %}}
The rate limit is enforced independently in each Dapr sidecar, and not cluster-wide.
{{% /alert %}}

Alternatively, the [max concurrency setting]({{< ref control-concurrency.md >}}) can be used to rate limit applications and applies to all traffic, regardless of remote IP, protocol, or path.

## Dapr configuration

To be applied, the middleware must be referenced in [configuration]({{< ref configuration-concept.md >}}). See [middleware pipelines]({{< ref "middleware.md#customize-processing-pipeline">}}).

```yaml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: appconfig
spec:
  httpPipeline:
    handlers:
    - name: ratelimit
      type: middleware.http.ratelimit
```

## Related links

- [Control max concurrently]({{< ref control-concurrency.md >}})
- [Middleware]({{< ref middleware.md >}})
- [Dapr configuration]({{< ref configuration-concept.md >}})
- [Configuration overview]({{< ref configuration-overview.md >}})
