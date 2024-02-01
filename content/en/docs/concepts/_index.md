---
title: Concepts
weight: 2
description: Understand the concepts of Falco Talon
---

# Concepts

* Tailor made for the Falco events
* No-code for the users
* UX close to Falco with the rules (yaml files with append, override mechanisms)
* Allow to set up sequential actions to run
* Structured logs (with a trace id)
* Helm chart
* The actions are triggered if match:
  * Falco rule name `(=)`
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

Represent a possible threat or suspicious action detected by `Falco` and sent to its outputs.

### Rule

The rule define the criterias for linking the events with the actions to run.

### Action

Each rule can sequentially run actions, each action refers to an actionner.

### Actionner

It defines what to the action will do.

### Notifier

It defines what outputs to notify with the result of the action.
