---
title: Traces
description: Traces
categories: [Examples]
tags: [test, sample, docs]
---

## OTEL collector

Falco Talon uses Open Telemetry SDK to send traces to Open Telemetry Collector.

Using OTEL collector allow users to send traces to whatever backend they prefer, other than doing batches agregations and taking advantages of other [OTEL features.](https://opentelemetry.io/docs/what-is-opentelemetry/)

## Enabling and customizing

You can see in the [docs](../configuration/_index.md) how to enable and customize the forwarding to OTEL, but essentially you simply need to enable it in your config:
```yaml
otel:
  enabled: true
```

That will, by default, send logs over gRPC to your collector on localhost:4317.

## Sample OTEL collector configuration

Here we have a sample OTEL configuration that sends traces to Jaeger and Grafana Tempo, please visit [OTEL documentation](https://opentelemetry.io/docs/collector/configuration/#exporters) to see the full list of available exporters:
```yaml
extensions:
  basicauth/otlp:
    client_auth:
      username: $GRAFANA_SAAS_USERNAME
      password: $GRAFANA_SAAS_TOKEN

receivers:
  otlp:
    protocols:
      grpc:
      http:
        cors:
          allowed_origins:
            - http://*
            - https://*

exporters:
  otlphttp:
    auth:
      authenticator: basicauth/otlp
    endpoint: $GRAFANA_SAAS_ENDPOINT
  otlp/jaeger:
    endpoint: "http://jaeger:4317"
    tls:
      insecure: true
processors:
  batch:

service:
  extensions:
    - basicauth/otlp
  telemetry:
    logs:
      level: "debug"
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [otlp/jaeger, otlphttp]
      processors: [batch]
```

## Results

After enabling OTEL forward in Talon and configuring your OTEL collector to send to your preferred backend, you should see traces in your tracing backend, in this example, we are using Jaeger:
![Jaeger](/static/images/jaeger.png?raw=true "Jaeger")

Here we have traces for all the main steps of Talon, all of them enriched with extra information:
![Jaeger Enriched](/static/images/jaeger_enriched.png?raw=true "Jaeger Enriched")
