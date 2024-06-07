---
title: Actionners
weight: 5
description: >
  Actionners are the built-it actions to react to the events
---

The `Actionners` define the actions to apply when an event matches a rule, they are named with pattern `category:action`.

The `category` allows to group `actions` and avoid multiple initializations (eg, multi Kubernetes API client, multi AWS clients, ...).

Each `actionner` is configured with `parameters`, a map of values passed to the action. The values can be a string, a list (array) or a map (map[string]string). 

Example: 
```yaml
- action: Terminate the pod
  actionner: kubernetes:terminate
  parameters:
    grace_period_seconds: 5
    ignore_daemonsets: true
    ignore_statefulsets: true
    min_healthy_replicas: 33%

- action: Label the pod
  actionner: kubernetes:label
  parameters:
    labels:
      suspicious: true

- action: Run Lambda
  actionner: aws:lambda
  parameters:
    aws_lambda_name: my-function
    aws_lambda_alias_or_version: $LATEST
  contexts:
    - aws
    - k8snode
```

{{% alert title="Warning" color="warning" %}}
Some actionners have by default the `Continue: false` setting by default, this stops the evaluation of the next actions of the rule. It can be overridden.
{{% /alert %}}

### Contexts

Some `actionners` allow to specify which sources to use to inject elements in the `context` field of the event payload, before triggering the action.

### aws

{{% alert title="Warning" color="warning" %}}
You must configure the `aws` section of the config.yaml to enable this injection.
{{% /alert %}}

Elements:
  - `aws.instance_profile_arn`
  - `aws.instance_profile_id`
  - `aws.region`

### k8snode

Elements:
  - `node.hostname`
  - `node.instancetype`
  - `node.role`
  - `node.topology.region`
  - `node.topology.zone`
  - `node.spec.providerid`
