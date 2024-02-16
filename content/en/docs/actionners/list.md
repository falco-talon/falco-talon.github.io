---
title: List of Actionners
weight: 5
description: >
  Available actionners
---

The `required fields` are the field elements that must be present in your Falco events to allow the actionner to do its work.

## `kubernetes`

The category `kubernetes` can be initialized with a `kubeconfig` file when Falco Talon runs outside Kubernetes.

### `kubernetes:terminate`

* Description: **Terminate the pod**
* Continue: `false`
* Parameters:
  * `grace_period_seconds`: The duration in seconds before the pod should be deleted. The value zero indicates delete immediately.
  * `ignore_daemonsets`: If true, the pods which belong to a Daemonset are not terminated.
  * `ignore_statefulsets`: If true, the pods which belong to a Statefulset are not terminated.
  * `min_healthy_replicas`: Minimum number of healthy pods to allow the termination, can be an absolute or % value (the value must be a quoted string).
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Source: `syscalls`

Example:
```yaml
- action: Terminate the pod
  actionner: kubernetes:terminate
  parameters:
    grace_period_seconds: 5
    ignore_daemonsets: true
    ignore_statefulsets: true
    min_healthy_replicas: 33%
```

### `kubernetes:labelize`

* Description: **Add, modify or delete the labels of the pod**
* Continue: `true`
* Parameters: 
  * `labels`: key:value map of labels to add/modify/delete (empty value means label deletion)
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Source: `syscalls`

Example:
```yaml
- action: Labelize the pod
  actionner: kubernetes:labelize
  parameters:
    labels:
      suspicious: true
```

### `kubernetes:networkpolicy`

* Description: **Create, update a network policy to block all egress traffic for pod**
* Continue: `true`
* Parameters:
  * `allow`: list of CIDR to allow anyway (eg: private subnets)
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Source: `syscalls`

Example:
```yaml
- action: Create a network policy
  actionner: kubernetes:networkpolicy
  parameters:
    allow:
      - 192.168.1.0/24
      - 172.17.0.0/16
```

### `kubernetes:exec`

* Description: **Exec a command in a pod**
* Continue: `true`
* Parameters:
  * `shell`: SHELL used to run the command (default: `/bin/sh`)
  * `command` Command to run
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Source: `syscalls`

Example:
```yaml
- action: Exec a command into the pod
  actionner: kubernetes:exec
  parameters:
    shell: /bin/bash
    command: "ps awxuf"
```

### `kubernetes:script`

* Description: **Run a script in a pod**
* Continue: `true`
* Parameters:
  * `shell`: SHELL used to run the script (default; `/bin/sh`)
  * `script`: Script to run (use `|` to use multilines) (can't be used at the same time than `file`)
  * `file`: Shell script file (can't be used at the same time than `script`)
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Source: `syscalls`

Example:
```yaml
- action: Run a script into the pod
  actionner: kubernetes:script
  parameters:
    shell: /bin/bash
    script: |
      ps awxuf
      netstat -lpauten
      top -n 1
```

### `kubernetes:log`

* Description: **Get logs from a pod**
* Continue: `true`
* Parameters:
  * `tail_lines`: The number of lines from the end of the logs to show (default: `1000`)
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Source: `syscalls`

Example:
```yaml
- action: Get logs of the pod
  actionner: kubernetes:log
  parameters:
    tail_lines: 200
```

### `kubernetes:delete`

* Description: **Delete the resource**
* Continue: `false`
* Parameters: `N/A`
* Required fields:
  * `ka.target.resource`
  * `ka.target.name`
  * `ka.target.namespace`
* Source: `k8saudit`

Example:
```yaml
- action: Delete the suspicious resource
  actionner: kubernetes:delete
  parameters:
    tail_lines: 200
```

{{% alert title="Info" color="info" %}}
The managed resources are:
- namespace
- configmap
- secret
- deployment
- daeomonset
- service
- serviceaccount  
- replicaset
- statefulset
- role
- clusterole
{{% /alert %}}

## `calico`

The category `calico` can be initialized with a `kubeconfig` file when Falco Talon runs outside Kubernetes.

### `calico:networkpolicy`

* Description: **Create a Calico Network Policy to block the egress traffic to a specific IP**
* Continue: `true`
* Parameters:
  * `allow`: list of CIDR to allow anyway (eg: private subnets) (default: 0.0.0.0/0)
  * `order`: order of the network policy
* Required fields:
  * `fd.sip` or `fd.rip`
* Source: `syscalls`

Example:
```yaml
- action: Create Calico netpol
  actionner: calico:networkpolicy
  parameters:
    order: 20
```