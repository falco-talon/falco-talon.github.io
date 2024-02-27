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

- [BPF Program Not Profiled](https://thomas.labarussias.fr/falco-rules-explorer/?source=syscalls&hash=1f6f21e3cf389d5b1970006c3acc1a92)
- [Service Account Created in Kube Namespace](https://thomas.labarussias.fr/falco-rules-explorer/?source=k8s_audit&hash=b5a8cad895c664de9e626483fe9bbc91)
- [Exfiltrating Artifacts via Kubernetes Control Plane](https://thomas.labarussias.fr/falco-rules-explorer/?source=syscalls&hash=895b0567a065496ab1c5e877c9afc8c7)

### Rule

The rule define the criteria for linking the events with the actions to run.

See [here](/docs/rules) to know more.

### Action

Each rule can sequentially run actions, each action refers to an actionner to know what to do.

See [here](/docs/rules/#action) to know more.

### Actionner

It defines what the action will do.

For example:
- [kubernetes:terminate](https://docs.falco-talon.org/docs/actionners/list/#kubernetesterminate)
- [kubernetes:networkpolicy](https://docs.falco-talon.org/docs/actionners/list/#kubernetesnetworkpolicy)
- [kubernetes:script](https://docs.falco-talon.org/docs/actionners/list/#kubernetesscript)

See [here](/docs/actionners) to know more.

### Notifier

It defines what outputs to notify with the result of the action.

See [here](/docs/notifiers) to know more.

### Deduplication

`Falco Talon` is able to de-duplicate the received events from Falco. It may happen `Falco` triggers the same rule multiple times for the same origin action (eg: change of thread.id) and `Falco Talon` can de-duplicate these events to avoid to run several times the same action.

The de-deduplication function can also work in a multi pods in Kubernetes context, `Falco Talon` will create a [lease](https://kubernetes.io/docs/concepts/architecture/leases/) to elect a leader that will aggregate all events from the other `Falco Talon` pods. In case of any issue, a new leader is automatically elected.

See [here](/docs/configuration/#configuration) for the settings.