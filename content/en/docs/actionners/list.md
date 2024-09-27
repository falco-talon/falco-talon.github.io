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

* Name: `terminate`
* Category: `kubernetes`
* Description: **Terminate the pod**
* Continue: `false`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

* `grace_period_seconds`: The duration in seconds before the pod should be deleted. The value zero indicates delete immediately.
* `ignore_daemonsets`: If true, the pods which belong to a Daemonset are not terminated.
* `ignore_statefulsets`: If true, the pods which belong to a Statefulset are not terminated.
* `min_healthy_replicas`: Minimum number of healthy pods to allow the termination, can be an absolute or % value (the value must be a quoted string).

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - delete
    - list
  - apiGroups:
    - apps
    resources:
    - replicasets
    verbs:
    - get
```

#### Example

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

* Name: `label`
* Category: `kubernetes`
* Description: **Add, modify or delete the labels of the pod**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

* `level`: level to apply the apply the labels, can be `node` or `pod` (default) 
* `labels`: (required) key:value map of labels to add/modify/delete (empty value means label deletion)

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - update
    - patch
    - list
```

#### Example
```yaml
- action: Label the pod
  actionner: kubernetes:label
  parameters:
    level: pod
    labels:
      suspicious: true
```

### `kubernetes:networkpolicy`

* Name: `networkpolicy`
* Category: `kubernetes`
* Description: **Create, update a network policy to block all egress traffic for pod**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

* `allow_cidr`: list of CIDR to allow anyway (eg: private subnets)
* `allow_namespaces`: list of namespaces to allow anyway

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - networking.k8s.io
    resources:
    - networkpolicies
    verbs:
    - get
    - update
    - patch
    - create
  - apiGroups:
    - apps
    resources:
    - daemonsets
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - deployments
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - replicasets
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - statefulsets
    verbs:
    - get
```

#### Example

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

* Name: `exec`
* Category: `kubernetes`
* Description: **Exec a command in a pod**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `true`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

* `shell`: SHELL used to run the command (default: `/bin/sh`)
* `command`: (required) Command to run

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - ""
    resources:
    - pods/exec
    verbs:
    - get
    - create
```

#### Example

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

* Name: `script`
* Category: `kubernetes`
* Description: **Run a script in a pod**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `true`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

* `shell`: SHELL used to run the script (default; `/bin/sh`)
* `script`: Script to run (use `|` to use multilines) (can't be used at the same time than `file`)
* `file`: Shell script file (can't be used at the same time than `script`)

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - ""
    resources:
    - pods/exec
    verbs:
    - get
    - create
```

#### Example

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

* Name: `log`
* Category: `kubernetes`
* Description: **Get logs from a pod**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `optionnal` (if no `output` is specified, the logs are printed in the log line)
* Source: `syscalls`

#### Parameters

* `tail_lines`: The number of lines from the end of the logs to show (default: `20`)

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - ""
    resources:
    - pods/log
    verbs:
    - get
```

#### Example

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

* Name: `download`
* Category: `kubernetes`
* Description: **Download a file from a pod**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `true`
* Output: `required`
* Source: `syscalls`

#### Parameters

* `file`: (required) The full path of the file to download

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - ""
    resources:
    - pods/exec
    verbs:
    - get
    - create
```

#### Example

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

* Name: `tcpdump`
* Category: `kubernetes`
* Description: **Capture the network packets for the pod**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `required`
* Source: `syscalls`

#### Parameters

* `duration`: duration in seconds of the capture (default: 5)
* `snaplen`: number of bytes captured for each packet (default: 4096)

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - update
    - patch
    - list
  - apiGroups:
    - ""
    resources:
    - pods/ephemeralcontainers
    verbs:
    - patch
    - create
  - apiGroups:
    - ""
    resources:
    - pods/exec
    verbs:
    - get
    - create
```

#### Example

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

* Name: `delete`
* Category: `kubernetes`
* Description: **Delete the resource**
* Continue: `false`
* Required fields:
  * `ka.target.resource`
  * `ka.target.name`
  * `ka.target.namespace`
* Use context: `false`
* Output: `n/a`
* Source: `k8saudit`

#### Parameters

N/A

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - namespaces
    verbs:
    - get
    - delete
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - delete
    - list
  - apiGroups:
    - apps
    resources:
    - daemonsets
    verbs:
    - get
    - delete
  - apiGroups:
    - apps
    resources:
    - deployments
    verbs:
    - get
    - delete
  - apiGroups:
    - apps
    resources:
    - replicasets
    verbs:
    - get
    - delete
  - apiGroups:
    - apps
    resources:
    - statefulsets
    verbs:
    - get
    - delete
  - apiGroups:
    - rbac.authorization.k8s.io
    resources:
    - roles
    verbs:
    - get
    - delete
  - apiGroups:
    - rbac.authorization.k8s.io
    resources:
    - clusterroles
    verbs:
    - get
    - delete
  - apiGroups:
    - ""
    resources:
    - configmaps
    verbs:
    - get
    - delete
  - apiGroups:
    - ""
    resources:
    - secrets
    verbs:
    - get
    - delete
```

#### Example

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

* Name: `cordon`
* Category: `kubernetes`
* Description: **Cordon a node**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

N/A

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - ""
    resources:
    - nodes
    verbs:
    - get
    - update
    - patch
```

#### Example

```yaml
- action: Cordon the node
  actionner: kubernetes:cordon
```

### `kubernetes:drain`

* Name: `drain`
* Category: `kubernetes`
* Description: **Drain a node**
* Continue: `true`
* Required fields:
  * `k8s.pod.name`
  * `k8s.ns.name`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

* `ignore_daemonsets`: If true, the pods which belong to a Daemonset are not terminated.
* `ignore_statefulsets`: If true, the pods which belong to a Statefulset are not terminated.
* `min_healthy_replicas`: Minimum number of healthy pods to allow the termination, can be an absolute or % value (the value must be a quoted string).
* `ignore_error`: If true, errors during the drain will be ignored, resulting in a successful action call. Used to control subsequent actions flow.
* `max_wait_period`: Amount of time to wait for eviction. If not set, the actionner will immediately return after calling the API for eviction.
* `wait_period_excluded_namespaces`: List of namespaces to exclude from the waiting period. If set, pods on those namespaces won't be part of the waiting process.

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - ""
    resources:
    - pods/eviction
    verbs:
    - get
    - create
  - apiGroups:
    - apps
    resources:
    - replicasets
    verbs:
    - get
```

#### Example

```yaml
- action: Drain the node
  actionner: kubernetes:drain
```

## `calico`

The category `calico` can be initialized with a `kubeconfig` file when Falco Talon runs outside Kubernetes.

### `calico:networkpolicy`

* Name: `networkpolicy`
* Category: `calico`
* Description: **Create a Calico Network Policy to block the egress traffic to a specific IP**
* Continue: `true`
* Required fields:
  * `fd.sip` or `fd.rip`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

* `allow_cidr`: list of CIDR to allow anyway (eg: private subnets) (default: 0.0.0.0/0)
* `allow_namespaces`: list of namespaces to allow anyway
* `order`: order of the network policy

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - projectcalico.org
    resources:
    - caliconetworkpolicies
    verbs:
    - get
    - update
    - patch
    - create
  - apiGroups:
    - apps
    resources:
    - daemonsets
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - deployments
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - replicasets
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - statefulsets
    verbs:
    - get
```

#### Example

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

## `cilium`

The category `cilium` can be initialized with a `kubeconfig` file when Falco Talon runs outside Kubernetes.

### `cilium:networkpolicy`

* Name: `networkpolicy`
* Category: `cilium`
* Description: **Create a Cilium Network Policy to block the egress traffic to a specific IP**
* Continue: `true`
* Required fields:
  * `fd.sip` or `fd.rip`
* Use context: `false`
* Output: `n/a`
* Source: `syscalls`

#### Parameters

* `allow_cidr`: list of CIDR to allow anyway (eg: private subnets) (default: 0.0.0.0/0)
* `allow_namespaces`: list of namespaces to allow anyway
* `order`: order of the network policy

#### Permissions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: falco-talon
rules:
  - apiGroups:
    - ""
    resources:
    - pods
    verbs:
    - get
    - list
  - apiGroups:
    - cilium.io
    resources:
    - ciliumnetworkpolicies
    verbs:
    - get
    - update
    - patch
    - create
  - apiGroups:
    - apps
    resources:
    - daemonsets
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - deployments
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - replicasets
    verbs:
    - get
  - apiGroups:
    - apps
    resources:
    - statefulsets
    verbs:
    - get
```

#### Example

```yaml
- action: Create Cilium netpol
  actionner: cilium:networkpolicy
  parameters:
    allow_cidr:
      - "192.168.1.0/24"
      - "172.17.0.0/16"
    allow_namespaces:
      - "green-ns"
      - "blue-ns"
```

## `aws`

### `aws:lambda`

* Name: `lambda`
* Category: `aws`
* Description: **Invoke an AWS lambda forwarding the Falco event payload**
* Continue: `true`
* Required AWS access:
  * `sts:getCallerIdentity`
  * `lambda:InvokeFunction`
  * `lambda:GetFunction`
* Use context: `true`
* Output: `n/a`
* Source: `any`

#### Parameters 

* `aws_lambda_name`: Lambda name to call. Lambda must reside in the same region as your default credential provider or static region provided in configuration.
* `aws_lambda_alias_or_version`: Lambda alias or version to call. (default: $LATEST)
* `aws_lambda_invocation_type`: Invocation type for Lambda. Accepted values: RequestResponse, Event, DryRun. (default: RequestResponse)

#### Permissions

```json
{
    "Version": "2012-10-17",
    "Sta<tement": [
        {
            "Sid": "AllowInvokeLambdaFunction",
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "arn:aws:lambda:<region>:<account_id>:function:<function_name>"
        },
        {
            "Sid": "AllowSTSGetCallerIdentity",
            "Effect": "Allow",
            "Action": "sts:GetCallerIdentity"
        }
    ]
}
```

#### Example

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