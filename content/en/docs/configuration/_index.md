---
title: Configuration
weight: 2
description: Configuration file
---

## Configuration

The static configuration of `Falco Talon` is set with a `.yaml` file (default: `./config.yaml`) or with environment variables.

|               Setting               |               Env var               |  Default  |                                   Description                                   |
| ----------------------------------- | ----------------------------------- | :-------: | ------------------------------------------------------------------------------- |
| `listen_address`                    | `LISTEN_ADDRESS`                    | `0.0.0.0` | Listten Address                                                                 |
| `listen_port`                       | `LISTEN_PORT`                       |  `2803`   | Listten Port                                                                    |
| `rules_files`                       | `RULES_FILES`                       |    n/a    | File with rules                                                                 |
| `watch_rules`                       | `WATCH_RULES`                       |  `true`   | Reload rules if they change                                                     |
| `print_all_events`                  | `PRINT_ALL_EVENTS`                  |  `true`   | Print in logs all received events, not only those which matches                 |
| `kubeconfig`                        | `KUBECONFIG`                        |    n/a    | Kube config file, only if `Falco Talon` runs outside Kubernetes                 |
| `log_format`                        | `LOG_FORMAT`                        |  `color`  | Log Format: text, color, json                                                   |
| `deduplication_leader_lease`        | `DEDUPLICATION_LEADER_LEASE`        |  `true`   | In k8s only, create a lease for the leader election, used for the deduplication |
| `deduplication_time_window_seconds` | `DEDUPLICATION_TIME_WINDOW_SECONDS` |    `5`    | Duration in seconds for the deduplication time window                           |
| `default_notifiers`                 | `DEFAULT_NOTIFIERS`                 |    n/a    | List of `notifiers` which are enabled for all rules                             |
| `notifiers_x`                       | `NOTIFIERS_X`                       |    n/a    | List of `notifiers` with their settings                                         |

#### Example

```yaml
listen_address: "0.0.0.0"
listen_port: "2803"
rules_files: "./rules.yaml"
kubeconfig: "./kubeconfig.yaml"

default_notifiers:
  - slack

notifiers:
  slack:
    webhook_url: "https://hooks.slack.com/services/XXXX"
    username: "Falco Talon"
    footer: ""
```

See [here](/docs/notifiers/list/) for the settings of the notifers.