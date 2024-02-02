---
title: Notifiers
weight: 5
description: >
  Notifiers are the built-it outputs to forward the result of the Falco Talon actions
---

The `Notifiers` define which outputs to notify with the result of the actions.

The configuration file contains the list of `notifiers` to enable by default and the settings for each `notifier`.


Example:
```yaml
default_notifiers: # this notifiers will be enabled for all rules
  - k8sevents
  - loki
  - smtp
  - elasticsearch

notifiers:
  loki:
    host_port: https://logs-prod-eu-west-0.grafana.net
    user: 99999
    api_key: xxxxx
  smtp:
    host_port: localhost:1025
    from: falco@falcosecurity.org
    to: user@test.com, other@test.com
    # user: test
    # password: test
    format: html
    tls: false
  elasticsearch:
    url: http://localhost:9200
    create_index_template: true
    number_of_shards: 1
    number_of_replicas: 1
```