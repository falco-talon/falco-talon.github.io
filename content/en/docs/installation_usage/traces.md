---
title: Traces
description: Traces
categories: [Examples]
tags: [test, sample, docs]
---

Falco Talon can export traces which are very useful to have observability over its performed actions.

![images/traces_grafana.png](../images/traces_grafana.png)

You can enable the traces by enabling them in the `config.yaml` and setting the address and port of the OTEL collector.

```yaml
otel:
  traces_enabled: true
  collector_port: 4317
  collector_endpoint: localhost
  collector_use_insecure_grpc: true
  timeout: 10
```

{{% alert title="Information" color="info" %}}
A trace is emitted for every events received by Falco Talon, you can configure the OTEL Collector to do some [tail sampling](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/processor/tailsamplingprocessor/README.md) and only store the traces with actions.

```yaml
...
processors:
  batch:
  tail_sampling:
    decision_wait: 1s
    num_traces: 200
    policies:
      [
        {
          name: ignore-unmatched,
          type: ottl_condition,
          ottl_condition: {
            error_mode: ignore,
            span: [
              "IsMatch(name, \"match\")"
            ]
          }
        }
      ]
service:
  pipelines:
    traces:
      ...
      processors: [tail_sampling, batch]
      ...
...
```
{{% /alert %}}
