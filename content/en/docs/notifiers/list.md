---
title: List of Notifiers
weight: 5
description: >
  Available notifiers
---

## K8s Events

This notifiers creates a [k8s event](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/##event-v1-events-k8s-io) in the target resource namespace.

#### Settings

No configuration is requested.

#### Result

```
LAST SEEN   TYPE     REASON                                    OBJECT                      MESSAGE
18s         Normal   falco-talon:kubernetes:labelize:success   pod/cncf-55696bc998-ql5qd   Status: success...
18s         Normal   falco-talon:kubernetes:labelize:success   pod/cncf-55696bc998-ql5qd   Status: success...
```

```yaml
action: kubernetes:labelize
apiVersion: v1
eventTime: "2024-02-27T13:54:30.008721Z"
firstTimestamp: null
involvedObject:
  kind: Pod
  name: cncf-55696bc998-ql5qd
  namespace: default
kind: Event
lastTimestamp: null
message: "Status: success\nMessage: action\nRule: Terminal shell in container\nAction:
  Labelize Pod as Suspicious\nActionner: kubernetes:labelize\nEvent: A shell was spawned
  in a container with an attached terminal (evt_type=execve user=root user_uid=0 user_loginuid=-1
  process=sh proc_exepath=/usr/bin/dash parent=runc command=sh -c command -v bash
  >/dev/null && exec bash || exec sh terminal=34817 exe_flags=EXE_WRITABLE container_id=f13c72275fd4
  container_image=docker.io/library/debian container_image_tag=latest container_name=cncf
  k8s_ns=default k8s_pod_name=cncf-55696bc998-ql5qd)\nnamespace: default\npod: cncf-55696bc998-ql5qd\nOutput:
  \nthe pod \"cncf-55696bc998-ql5qd\" in the namespace \"default\" has been labelized\nTraceID:
  cc84de57-7617-4359-ad40-1a7a8d854743\n"
metadata:
  creationTimestamp: "2024-02-27T13:54:30Z"
  generateName: falco-talon-
  name: falco-talon-rf2dz
  namespace: default
  resourceVersion: "32141229"
  uid: a407b246-f2b5-46ba-8146-ebe474302604
reason: falco-talon:kubernetes:labelize:success
reportingComponent: falcosecurity.org/falco-talon
reportingInstance: falco-talon
source:
  component: falco-talon
type: Normal
```

## Slack

#### Settings

|    Setting    |                                      Default                                      |        Description         |         |
| ------------- | --------------------------------------------------------------------------------- | -------------------------- | ------- |
| `webhook_url` | n/a                                                                               | Webhook URL                |         |
| `icon`        | `https://upload.wikimedia.org/wikipedia/commons/2/26/Circaetus_gallicus_claw.jpg` | Avatar for messages        |         |
| `username`    | `Falco Talon`                                                                     | Username for messages      |         |
| `footer`      | `https://github.com/Issif/falco-talon`                                            | Footer for messages        |         |
| `format`      | `long`                                                                            | Format for messages (`long | short`) |

#### Example

```yaml
notifiers:
  slack:
    webhook_url: "https://hooks.slack.com/services/XXXX"
    icon: "https://upload.wikimedia.org/wikipedia/commons/2/26/Circaetus_gallicus_claw.jpg"
    username: "Falco Talon"
    footer: "https://github.com/Falco-Talon/falco-talon"
    format: long
```

#### Results

with `format: short`:

![images/slack_short.png](../images/slack_short.png)

with `format: long`:

![images/slack_long.png](../images/slack_long.png)


## Loki

#### Settings

|     Setting      | Default |         Description          |
| ---------------- | ------- | ---------------------------- |
| `url`            | n/a     | http://{domain or ip}:{port} |
| `user`           | n/a     | User for Grafana Logs        |
| `api_key`        | n/a     | API Key for Grafana Logs     |
| `tenant`         | n/a     | Add the Tenant header        |
| `custom_headers` | n/a     | Custom HTTP Headers          |

#### Result

![images/loki_grafana.png](../images/loki_grafana.png)

#### Example

```yaml
notifiers:
  loki:
    host_port: "https://xxxxx"
    user: "xxxx"
    api_key: "xxxxx"
```

## Elasticsearch

#### Settings

|         Setting         |    Default    |                                    Description                                    |
| ----------------------- | ------------- | --------------------------------------------------------------------------------- |
| `host_port`             | n/a           | http://{domain or ip}:{port}                                                      |
| `user`                  | n/a           | User for Grafana Logs                                                             |
| `password`              | n/a           | Password for Grafana Logs                                                         |
| `index`                 | `falco-talon` | Elasticsearch index                                                               |
| `suffix`                | `daily`       | Date suffix for index rotation : `daily` (default), `monthly`, `annually`, `none` |
| `create_index_template` | `true`        | Create the index template at the init if it doesn't exist                         |
| `number_of_shards`      | `3`           | Number of shards for the index  (if `create_index_template` is `true`)            |
| `number_of_replicas`    | `3`           | Number of replicas for the index (if `create_index_template` is `true`)           |
| `custom_headers`        | n/a           | Custom HTTP Headers                                                               |

#### Example

```yaml
notifiers:
  elasticsearch:
    url: "http://localhost:9200"
    create_index_template: true
    number_of_shards: 1
    number_of_replicas: 1
```

## SMTP

#### Settings

|   Setting   | Default |              Description              |
| ----------- | ------- | ------------------------------------- |
| `host_port` | n/a     | Host:Port of SMTP server              |
| `user`      | n/a     | User for SMTP                         |
| `password`  | n/a     | Password for SMTP                     |
| `from`      | n/a     | From                                  |
| `to`        | n/a     | To (comma separated list of adresses) |
| `format`    | `html`  | Format of the email (`text`, `html`)  |
| `tls`       | `false` | Use TLS connection                    |

#### Example

```yaml
notifiers:
  smtp:
    host_port: "localhost:1025"
    from: "falco@falcosecurity.org"
    to: "user@test.com, other@test.com"
    user: "xxxx"
    password: "xxxxx"
    format: "html"
    tls: false
```

#### Results

with `format: html`:

![./images/smtp_html.png](../images/smtp_html.png)

with `format: text`:

![images/smtp_text.png](../images/smtp_text.png)

## Webhook

#### Settings

|     Setting      |              Default              |     Description     |
| ---------------- | --------------------------------- | ------------------- |
| `url`            | n/a                               | URL                 |
| `http_method`    | `POST`                            | HTTP Method         |
| `user_agent`     | `Falco-Talon`                     | User Agent          |
| `content_type`   | `application/json; charset=utf-8` | Content Type        |
| `custom_headers` | n/a                               | Custom HTTP Headers |

#### Example

```yaml
notifiers:
  webhook:
    url: "http://xxxx"
    http_method: "POST"
    user_agent: "Falco-Talon"
    content_type: "application/json; charset=utf-8"
    custom_headers:
      Authorization: "Bearer xxxxx"
```
