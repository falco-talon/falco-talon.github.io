---
title: List of Actionners
weight: 5
description: >
  Available actionners
---

The `required fields` are the field elements that must be present in your Falco event to allow the actionner to do its work.

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
* Use context: `false`
* Output: `n/a`
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

### `kubernetes:label`

* Description: **Add, modify or delete the labels of the pod**
* Continue: `true`
* Parameters: 
  * `level`: level to apply the apply the labels, can be `node` or `pod` (default) 
  * `labels`: (required) key:value map of labels to add/modify/delete (empty value means label deletion)
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

Example:
```yaml
- action: Label the pod
  actionner: kubernetes:label
  parameters:
    level: pod
    labels:
      suspicious: true
```

### `kubernetes:networkpolicy`

* Description: **Create, update a network policy to block all egress traffic for pod**
* Continue: `true`
* Parameters:
  * `allow_cidr`: list of CIDR to allow anyway (eg: private subnets)
  * `allow_namespaces`: list of namespaces to allow anyway
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

Example:
```yaml
- action: Create a network policy
  actionner: kubernetes:networkpolicy
  parameters:
    allow_cidr:
      - "192.168.1.0/24"
      - "172.17.0.0/16"
    allow_namespaces:
      - "green-ns"
      - "blue-ns"
```

### `kubernetes:exec`

* Description: **Exec a command in a pod**
* Continue: `true`
* Parameters:
  * `shell`: SHELL used to run the command (default: `/bin/sh`)
  * `command`: (required) Command to run
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `true`
* Output: `n/a`
* Source: `syscalls`

Example:
```yaml
- action: Exec a command into the pod
  actionner: kubernetes:exec
  parameters:
    shell: /bin/bash
    command: "cat ${FD_NAME}"
```

{{% alert title="Info" color="info" %}}
For the available contexts, see [here](/docs/actionners/contexts).
{{% /alert %}}

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
* Use context: `true`
* Output: `n/a`
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
      cat ${FD_NAME}
```

{{% alert title="Info" color="info" %}}
For the available contexts, see [here](/docs/actionners/contexts).
{{% /alert %}}

### `kubernetes:log`

* Description: **Get logs from a pod**
* Continue: `true`
* Parameters:
  * `tail_lines`: The number of lines from the end of the logs to show (default: `20`)
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `optionnal` (if no `output` is specified, the logs are printed in the log line)
* Source: `syscalls`

Example:
```yaml
- action: Get logs of the pod
  actionner: kubernetes:log
  parameters:
    tail_lines: 200
  output:
    target: aws:s3
    parameters:
      bucket: my-bucket
      prefix: /logs/
```

### `kubernetes:download`

* Description: **Download a file from a pod**
* Continue: `true`
* Parameters:
  * `file`: (required) The full path of the file to download
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `true`
* Output: `required`
* Source: `syscalls`

Example:
```yaml
- action: Get logs of the pod
  actionner: kubernetes:download
  parameters:
    tail_lines: 200
  output:
    target: aws:s3
    parameters:
      bucket: my-bucket
      prefix: /files/
```

### `kubernetes:tcpdump`

* Description: **Capture the network packets for the pod**
* Continue: `true`
* Parameters:
  * `duration`: duration in seconds of the capture (default: 5)
  * `snaplen`: number of bytes captured for each packet (default: 4096)
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `required`
* Source: `syscalls`

Example:
```yaml
- action: Get logs of the pod
  actionner: kubernetes:tcpdump
  parameters:
    duration: 10
    snaplen: 1024
  output:
    target: aws:s3
    parameters:
      bucket: my-bucket
      prefix: /captures/
```

### `kubernetes:delete`

* Description: **Delete the resource**
* Continue: `false`
* Parameters: `N/A`
* Required fields:
  * `ka.target.resource`
  * `ka.target.name`
  * `ka.target.namespace`
* Use context: `false`
* Output: `n/a`
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
- daemonset
- service
- serviceaccount  
- replicaset
- statefulset
- role
- clusterole
{{% /alert %}}


### `kubernetes:cordon`

* Description: **Cordon a node**
* Continue: `true`
* Parameters: N/A
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

Example:
```yaml
- action: Cordon the node
  actionner: kubernetes:cordon
```

### `kubernetes:drain`

* Description: **Drain a node**
* Continue: `true`
* Parameters:
  * `grace_period_seconds`: The duration in seconds before the pod should be deleted. The value zero indicates delete immediately.
  * `ignore_daemonsets`: If true, the pods which belong to a Daemonset are not terminated.
  * `ignore_statefulsets`: If true, the pods which belong to a Statefulset are not terminated.
  * `min_healthy_replicas`: Minimum number of healthy pods to allow the termination, can be an absolute or % value (the value must be a quoted string).
  * `ignore_error`: If true, errors during the drain will be ignored, resulting in a successful action call. Used to control subsequent actions flow.
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

Example:
```yaml
- action: Drain the node
  actionner: kubernetes:drain
```

## `calico`

The category `calico` can be initialized with a `kubeconfig` file when Falco Talon runs outside Kubernetes.

### `calico:networkpolicy`

* Description: **Create a Calico Network Policy to block the egress traffic to a specific IP**
* Continue: `true`
* Parameters:
  * `allow_cidr`: list of CIDR to allow anyway (eg: private subnets) (default: 0.0.0.0/0)
  * `allow_namespaces`: list of namespaces to allow anyway
  * `order`: order of the network policy
* Required fields:
  * `fd.sip` or `fd.rip`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

Example:
```yaml
- action: Create Calico netpol
  actionner: calico:networkpolicy
  parameters:
    order: 20
    allow_cidr:
      - "192.168.1.0/24"
      - "172.17.0.0/16"
    allow_namespaces:
      - "green-ns"
      - "blue-ns"
```

### `aws:lambda`

* Description: **Invoke an AWS lambda forwarding the Falco event payload**
* Continue: `true`
* Parameters:
  * `aws_lambda_name`: Lambda name to call. Lambda must reside in the same region as your default credential provider or static region provided in configuration.
  * `aws_lambda_alias_or_version`: Lambda alias or version to call. (default: $LATEST)
  * `aws_lambda_invocation_type`: Invocation type for Lambda. Accepted values: RequestResponse, Event, DryRun. (default: RequestResponse)
* Required AWS access:
  * `sts:getCallerIdentity`
  * `lambda:InvokeFunction`
  * `lambda:GetFunction`
* Use context: `true`
* Output: `n/a`
* Source: `any`

Example:
```yaml
- action: Invoke Lambda function
  actionner: aws:lambda
  parameters:
    aws_lambda_name: sample-function
    aws_lambda_alias_or_version: $LATEST
    aws_lambda_invocation_type: RequestResponse
```

{{% alert title="Info" color="info" %}}
For the available contexts, see [here](/docs/actionners/contexts).
{{% /alert %}}