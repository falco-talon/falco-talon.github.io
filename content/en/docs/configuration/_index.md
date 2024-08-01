---
title: Configuration
weight: 2
description: Configuration file
---

## Configuration

The static configuration of `Falco Talon` is set with a `.yaml` file (default: `./config.yaml`) or with environment variables.

|               Setting               |               Env var               |  Default  |                                                 Description                                                 |
| ----------------------------------- | ----------------------------------- | :-------: | ----------------------------------------------------------------------------------------------------------- |
| `listen_address`                    | `LISTEN_ADDRESS`                    | `0.0.0.0` | Listten Address                                                                                             |
| `listen_port`                       | `LISTEN_PORT`                       |  `2803`   | Listten Port                                                                                                |
| `rules_files`                       | `RULES_FILES`                       |    n/a    | File with rules                                                                                             |
| `watch_rules`                       | `WATCH_RULES`                       |  `true`   | Reload rules if they change                                                                                 |
| `print_all_events`                  | `PRINT_ALL_EVENTS`                  |  `true`   | Print in logs all received events, not only those which matches                                             |
| `kubeconfig`                        | `KUBECONFIG`                        |    n/a    | Kube config file, only if `Falco Talon` runs outside Kubernetes                                             |
| `log_format`                        | `LOG_FORMAT`                        |  `color`  | Log Format: text, color, json                                                                               |
| `deduplication.leader_election`     | `DEDUPLICATION_LEADER_ELECTION`     |  `true`   | enable the leader election for cluster mode (in k8s only)                                                   |
| `deduplication.time_window_seconds` | `DEDUPLICATION_TIME_WINDOW_SECONDS` |    `5`    | Duration in seconds for the deduplication time window                                                       |
| `default_notifiers`                 | `DEFAULT_NOTIFIERS`                 |    n/a    | List of `notifiers` which are enabled for all rules                                                         |
| `notifiers_x`                       | `NOTIFIERS_X`                       |    n/a    | List of `notifiers` with their settings                                                                     |
| `aws.role_arn`                      | `AWS_ROLE_ARN`                      |    n/a    | AWS Role ARN to use with AWS actions                                                                        |
| `aws.external_id`                   | `AWS_ROLE_EXTERNAL_ID`              |    n/a    | AWS External ID used to assume roles with AWS actions. This field is ignored if **aws.role_arn** is not set |
| `aws.region`                        | `AWS_REGION`                        |    n/a    | AWS Region to use, it should be specified along **aws.access_key** and **aws.secret_key**                   |
| `aws.access_key`                    | `AWS_ACCESS_KEY`                    |    n/a    | AWS Access Key to use, it should be specified along **aws.region** and **aws.secret_key**                   |
| `aws.secret_key`                    | `AWS_SECRET_KEY`                    |    n/a    | AWS Secret Key to use, it should be specified along **aws.region** and **aws.access_key**                   |
| `minio.endpoint`                    | `MINIO_ENDPOINT`                    |    n/a    | Minio endpoint address                                                                                      |
| `minio.access_key`                  | `MINIO_ACCESS_KEY`                  |    n/a    | Access Key to use, it should be specified along **minio.secret_key**                                        |
| `minio.secret_key`                  | `MINIO_SECRET_KEY`                  |    n/a    | Secret Key to use, it should be specified along **minio.access_key**                                        |
| `minio.use_ssl`                     | `MINIO_USE_SSL`                     |  `false`  | Use SSL                                                                                                     |
| `otel.enabled`                  | `OTEL_ENABLED`                  |    `false`    |  Whether or not to enable OTEL collector trace forwarding                                       |
| `otel.collector_port`                  | `OTEL_COLLECTOR_PORT`                  |    `4317`    |  Port to forward OTEL collector traces                                       |
| `otel.collector_endpoint`                  | `OTEL_COLLECTOR_ENDPOINT`                  |    `localhost`    |  Endpoint to forward OTEL collector traces                                       |
| `otel.collector_use_insecure_grpc`                  | `OTEL_COLLECTOR_USE_INSECURE_GRPC`                  |    `false`    |  Whether or not to use insecure gRPC when forwarding traces to OTEL collector                                       |

#### Example

```yaml
listen_address: "0.0.0.0" # default: "0.0.0.0"
listen_port: "2803" # default: "2803"
rules_file:
  - "./rules.yaml" # default: "./rules.yaml"
# kubeConfig: "~/.kube/config" # only if Falco Talon is running outside Kubernetes
log_format: "color" # log Format: text, color, json (default: color)
watch_rules: true # reload if the rules file changes (default: true)
print_all_events: true # print in logs all received events, not only those which match

deduplication:
  leader_election: true # enable the leader election for cluster mode (in k8s only)
  time_window_seconds: 5 # duration in seconds for the deduplication time window (default: 5)

default_notifiers: # these notifiers will be enabled for all rules
  - k8sevents

aws:
  role_arn: arn:aws:iam::<account_number>:role/<role_name> # role to assume
  external_id: <external_id> # external id
  region: <region> # if not specified, default region from provider credential chain will be used
  access_key: <access_key> # if not specified, default access_key from provider credential chain will be used
  secret_key: <secret_key> # if not specified, default secret_key from provider credential chain will be used

otel:
  enabled: false
  collector_port: 4317
  collector_endpoint: localhost
  collector_use_insecure_grpc: false

minio:
  endpoint: <endpoint> # endpoint
  access_key: <access_key> # access key
  secret_key: <secret_key> # secret key
  use_ssl: false # use SSL

notifiers:
  slack:
    webhook_url: "https://hooks.slack.com/services/XXXX" # Slack webhook
    icon: "" # default: "https://upload.wikimedia.org/wikipedia/commons/2/26/Circaetus_gallicus_claw.jpg"
    username: "" # default: "Falco Talon"
    footer: "" # default: "https://github.com/falco-talon/falco-talon"
    format: long # default: long
  webhook:
    url: "" # address of the webhook endpoint
  smtp:
    host_port: "" # host:port of the SMTP server
    from: "" # from header
    to: "" # to
    user: "" # user
    password: "" # password
    format: "html" # format: html or text
```

See [here](/docs/notifiers/list/) for the settings of the notifers.