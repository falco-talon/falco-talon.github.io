---
title: Rules
weight: 4
description: >
  The rules define the mapping between the Falco events and the actions to run
---

{{% alert title="Info" color="info" %}}
The rules are evaluated from top to bottom.

Multiple rules files can be used (repeat the `-r` flag), the first file is overriden by the following ones (strings are replaced, lists are appended, ...).
{{% /alert %}}

{{% alert title="Info" color="info" %}}
If the setting `watch_rules` is set to `true`, `Falco Talon` watches the rules updates and automatically hot reloads them.
{{% /alert %}}


## Syntax

The syntax for the rules files is:

```yaml
- action: <string,required>
  description: <string>
  actionner: <string,required>
  continue: <bool>
  ignore_errors: <bool>
  parameters:
    <string>: <string>
    <string>:
      - <string>
      - <string>
    <string>:
      <string>: <string>
      <string>: <string>

- rule: <string,required>
  description: <string>
  match:
    rules:
      - <string>
      - <string>
    priority: <string>
    tags:
      - <string>, <string>, <string>
      - <string>, <string>
    output_fields:
      - <string>=<string>, <string>=<string>
      - <string>!=<string>, <string>=<string>
  continue: <bool>
  dry_run: <bool>
  actions:
    - action: <string,required>
    - action: <string,required>
      description: <string>
      actionner: <string,required>
      continue: <bool>
      ignore_errors: <bool>
      parameters:
        <string>: <any>
        <string>:
          - <any>
          - <any>
        <string>:
          <string>: <any>
          <string>: <any>
      output:
        target: <string,required>
        parameters:
          <string>: <any>
          <string>: <any>
  notifiers:
    - <string>
    - <string>
```

The rules files contain 2 types of blocks: 
* `action`: defines an action that can be reused by different rules
* `rule`: defines a rule to match with events and run actions

## Action

For the `action` block, the settings are:
* `action`: (*required*) name of action to trigger
* `description`: description of the action (for user only)
* `actionner`: name of the actionner to use
* `continue`: if `true`, no more action are applied after this one (each actionner has its own default value)
* `ignore_errors`: if `true`, ignore the errors and avoid to stop at this action.
* `parameters`: key:value map of parameters for the action. value can be a string, an array (slice) or a map
* `output`: defines where to store the artifact that might have been created/downloaded by the actionner
  * `target`: the name of the target used as output
  * `parameters`: key:value map of parameters for the output. value can be a string, an array (slice) or a map

## Rule

For the `rule` block, the settings are:
* `rule`: (*required*) Name of your rule
* `description`: description of the action (for user only)
* `match`: the section to define the criterias to match
  * `rules`: (*list*) (`OR` logic) Falco rules to match. If empty, all rules match.
  * `priority`: Priority to match. If empty, all priorities match. Syntax is like: `>=Critical`, `<Warning`, `Debug`
  * `tags`: (*list*) (`OR` logic) Comma separated lists of Tags to match (`AND` logic). If empty, all tags match.
  * `output_fields`: (*list*) (`OR` logic) Comma separated lists of key:comparison:value for Output fields to match (`AND` logic). If emtpy, all output fields match
* `actions`: the list of actions to sequentially run, they can refer to an `action` block or be defined locally 
  * `action`: (*required*) name of action to trigger, can refer to an `action` block
  * `description`: description of the action (for user only)
  * `actionner`: name of the actionner to use
  * `continue`: if `true`, no more action are applied after this one (each actionner has its own default value)
  * `ignore_errors`: if `true`, ignore the errors and avoid to stop at this action
  * `parameters`: key:value map of parameters for the action. value can be a string, an array (slice) or a map
  * `output`: defines where to store the artifact that might have been created/downloaded by the actionner
    * `target`: the name of the target used as output
    * `parameters`: key:value map of parameters for the output. value can be a string, an array (slice) or a map
* `continue`: if `true`, no more rule are compared after the rule has been triggered (default is `true`)
* `dry_run`: if `true`: the actions are not ran (default: `false`)
* `notifiers`: list of notifiers to enabled for the action, in addition with the defaults

## Example

In the below YAML manifest, we can see two different Talon actions - ```Terminate Pod``` and ```Disable outbound connections```.
1. The ```Terminate Pod``` action has a matching actionner to gracefully terminate a running workload with ```kubernetes:terminate```.
2. The ```Disable outbound connections``` action enforces a Kubernetes NetworkPolicy through ```kubernetes:networkpolicy```.

Finally, the two actions are initiated as instant response actions when the matching Falco rule [Unexpected outbound connection destination](https://thomas.labarussias.fr/falco-rules-explorer/?hash=8737710b2ecff31abe493a9496a76939) is triggered.

```yaml
- action: Terminate Pod
  description: terminate the pod if it doesn't belong to a statefulset
  actionner: kubernetes:terminate
  parameters:
    ignoreDaemonsets: false
    ignoreStatefulsets: true

- action: Disable outbound connections
  actionner: kubernetes:networkpolicy
  parameters:
    allow:
      - "192.168.1.0/24"
      - "172.17.0.0/16"
      - "10.0.0.0/32"

- rule: Suspicious outbound connection
  description: Block suspicious outbound connections and terminate the pod
  match:
    rules:
      - Unexpected outbound connection destination
  actions:
    - action: Get last logs
      actionner: kubernetes:log
      parameters:
        tail_lines: 10
      output:
        target: aws:s3
        parameters:
          bucket: my-bucket
          prefix: /logs/
    - action: Disable outbound connections
      ignore_errors: true
    - action: Terminate Pod # ref to a re-usable action
      parameters:
        gracePeriods: 2
```
