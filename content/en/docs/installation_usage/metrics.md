---
title: Metrics
description: Metrics
categories: [Examples]
tags: [test, sample, docs]
---

## Prometheus metrics

Falco Talon exposes a `/metrics` endpoint with some metrics in the Prometheus format.

```
# HELP action_total number of actions
# TYPE action_total counter
action_total{action="Disable outbound connections",actionner="kubernetes:networkpolicy",event="Test logs",namespace="falco",otel_scope_name="github.com/Falco-Talon/falco-talon",otel_scope_version="devel",pod="falco-5b7kc",rule="Suspicious outbound connection",status="failure"} 6
action_total{action="Terminate Pod",actionner="kubernetes:terminate",event="Test logs",namespace="falco",otel_scope_name="github.com/Falco-Talon/falco-talon",otel_scope_version="devel",pod="falco-5b7kc",rule="Suspicious outbound connection",status="failure"} 6
# HELP event_total number of received events
# TYPE event_total counter
event_total{event="Unexpected outbound connection destination",otel_scope_name="github.com/Falco-Talon/falco-talon",otel_scope_version="devel",priority="Critical",source="syscalls"} 2
# HELP match_total number of matched events
# TYPE match_total counter
match_total{event="Unexpected outbound connection destination",otel_scope_name="github.com/Falco-Talon/falco-talon",otel_scope_version="devel",priority="Critical",rule="Suspicious outbound connection",source="syscalls"} 2
```

## OTEL metrics

If you use OTEL Collector, you can configure Falco Talon to push its metrics to:

```yaml
otel:
  metrics_enabled: true
  collector_port: 4317
  collector_endpoint: localhost
  collector_use_insecure_grpc: true
  timeout: 10
```