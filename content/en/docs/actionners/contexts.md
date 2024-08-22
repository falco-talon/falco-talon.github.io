---
title: Contexts
weight: 10
description: >
  Available contexts for the actionners
---

The `context` is composed of elements from the original `Falco` event, from `Falco Talon` and other sources, these elements can be used to dynamically configure the `actions` to perform. They are all exposed as environment variables that can be used as tokens to replace in the `parameters` of the `actions`.

Except those from `Falco`, all the informations are also injected into a `context` section in the original payload (JSON) before the run of the action, it's useful for the `actionners` using a third party system (ie: `aws:lambda`) to have the original `Falco` event and the context of what `Falco Talon` did.

Example:
```json
{
    "output": "14:37:27.505989596: Warning Detected ptrace PTRACE_ATTACH attempt (proc_pcmdline=%proc.pcmdline evt_type=%evt.type user=%user.name user_uid=%user.uid user_loginuid=%user.loginuid process=%proc.name proc_exepath=%proc.exepath parent=%proc.pname command=%proc.cmdline terminal=%proc.tty exe_flags=%evt.arg.flags %container.info)",
    "priority": "WARNING",
    "rule": "PTRACE attached to process",
    "time": "2023-12-20T14:37:27.505989596Z",
    "output_fields": {
        "container.info": "container.info",
        "evt.arg.flags": "evt.arg.flags",
        "evt.type": "evt.type",
        "proc.cmdline": "proc.cmdline",
        "proc.exepath": "proc.exepath",
        "proc.name": "proc.name",
        "proc.pcmdline": "proc.pcmdline",
        "proc.pname": "proc.pname",
        "proc.tty": "proc.tty",
        "user.loginuid": "user.loginuid",
        "user.name": "user.name",
        "user.uid": "user.uid"
    },
    "hostname": "host-7.local",
    "source": "syscalls",
    "tags": [
        "maturity_stable",
        "host",
        "container",
        "process",
        "mitre_privilege_escalation",
        "T1055.008"
    ],
    "context": {
        "falco-talon.rule": "myrule",
        "falco-talon.rule.continue": true,
        "falco-talon.rule.dryrun": false,
        "falco-talon.rule.action": "myaction",
        "falco-talon.rule.actionner": "category:action",
        "falco-talon.rule.action.continue": true,
        "falco-talon.rule.action.ignore_errors": false,
        "falco-talon.rule.action.parameters": "{\"param1\": true, \"param2\": \"value2\", \"param3\": 10}",
    }
}
```

{{% alert title="Info" color="info" %}}
Having the information exposed as environment variables allows to use them as tokens to replace in some actionners like `kubernetes:script`, `kubernetes:download`, ...
Their presences in the `context` section of the payload (JSON) is useful for the actionners based on third parties like `aws:lambda`.
{{% /alert %}}

### Falco

The exposed env vars concerning `Falco` are exported as environment variables:
- `PRIORITY`: the priority of the Falco events
- `HOSTNAME`: the hostname where the Falco event occured
- `SOURCE`: the source for the Falco event
- `RULE`: the name of the rule that created the Falco event
- `TAGS`: a comma separated list of the tags associated with the Falco event
- All the `OutputFields` of the Falco event are exported, in uppercase, with the dots replaced by `_` and the brackets `[ ]` removed, examples:
  - `fd.name` > `FD_NAME`
  - `proc.args[0]` > `PROC_ARGS_0`

### Falco Talon

The exposed env vars concerning `Falco` are:
- `FALCO-TALON_RULE`: the name of the Falco Talon rule that matches
- `FALCO-TALON_RULE_CONTINUE`: if the Falco Talon rule allows to continue or not
- `FALCO-TALON_RULE_DRYRUN`: if the Falco Talon rule dry runs or not
- `FALCO-TALON_RULE_ACTION`: the name of the action triggered by the Falco Rule
- `FALCO-TALON_RULE_ACTION_CONTINUE`: if the action triggered by the Falco Rule allows to continue or not
- `FALCO-TALON_RULE_ACTION_IGNORE_ERRORS`: if the action triggered by the Falco Rule ignores the errors or not
- `FALCO-TALON_RULE_ACTION_PARAMETERS`: a json payload with parameters of the action triggered by the Falco Rule
- `FALCO-TALON_RULE_ACTIONNER`: the actionner used by the action triggered by the Falco Rule

Moreover, some `actionners` allow also to specify extra sources of information. 

### Other sources (optionnal)

#### AWS

For injecting some informations retrieved from AWS IMDS, use the source: `aws` (lower case).

In the `context` section of the payload:
```json
    "context": {
        "aws.intance.profile": "myprofile",
        "aws.instance.profile.id": "myprofileid",
        "aws.region": "us-east-1",
    }
```

As env vars:
  - `AWS_INSTANCE_PROFILE_ARN`
  - `AWS_INSTANCE_PROFILE_ID`
  - `AWS_REGION`

#### k8snode

For injecting some informations retrieved from the node, use the source: `k8snode`.

In the `context` section of the payload:
```json
    "context": {
        "node.hostname": "hostname",
        "node.instancetype": "instancetype",
        "node.role": "worker",
        "node.topology.zone": "zonea",
        "node.topology.region": "region",
        "node.spec.providedid": "id",
    }
```

As env vars:
  - `NODE_HOSTNAME`
  - `NODE_INSTANCETYPE`
  - `NODE_ROLE`
  - `NODE_TOPOLOGY_ZONE`
  - `NODE_TOPOLOGY_REGION`
  - `NODE_SPEC_PROVIDEDID`
