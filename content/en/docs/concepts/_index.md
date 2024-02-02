---
title: Concepts
weight: 2
description: Understand the concepts of Falco Talon
---

# Why we created Talon

* Custom-designed for immediate response to Falco events.
* Implement response actions without coding.
* User experience aligned with Falco, utilizing YAML for rule customization.
* Enables configuration of action sequences triggered by Falco rules.
* Provides structured logging complete with trace identification.
* Simplified deployment through Helm charts.
* Actions can be triggered on the below Falco criteria matches:
  * rule name `(=)`
  * priority (`=`, `>=`)
  * tags (`=`)
  * output fields (`=`, `!=`)

## Architecture

`Falco Talon` can receive the `events` from [`Falco`](https://falco.org) or [`Falcosidekick`](https://github.com/falcosecurity/falcosidekick):


```
┌──────────┐      ┌───────────────┐      ┌─────────────┐
│  Falco   ├──────► Falcosidekick ├──────► Falco Talon │
└──────────┘      └───────────────┘      └─────────────┘
or
┌──────────┐      ┌─────────────┐
│  Falco   ├──────► Falco Talon │
└──────────┘      └─────────────┘
```

## Keywords

### Event

Represents a possible threat or suspicious action detected by `Falco` and is sent to the outputs.

- [BPF Program Not Profiled](https://thomas.labarussias.fr/falco-rules-explorer/?source=syscalls&hash=1f6f21e3cf389d5b1970006c3acc1a92){:target="_blank"}
- [Service Account Created in Kube Namespace](https://thomas.labarussias.fr/falco-rules-explorer/?source=k8s_audit&hash=b5a8cad895c664de9e626483fe9bbc91){:target="_blank"}
- [Exfiltrating Artifacts via Kubernetes Control Plane](https://thomas.labarussias.fr/falco-rules-explorer/?source=syscalls&hash=895b0567a065496ab1c5e877c9afc8c7){:target="_blank"}

### Rule

The rule define the criteria for linking events with the actions to run.

### Action

Each rule can sequentially run actions, each action refers to an actionner.

### Actionner

It defines what to the action will do.

- [kubernetes:terminate](https://docs.falco-talon.org/docs/actionners/list/#kubernetesterminate){:target="_blank"}
- [kubernetes:networkpolicy](https://docs.falco-talon.org/docs/actionners/list/#kubernetesnetworkpolicy){:target="_blank"}
- [kubernetes:script](https://docs.falco-talon.org/docs/actionners/list/#kubernetesscript){:target="_blank"}

### Notifier

It defines what outputs to notify with the result of the action.
