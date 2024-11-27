---
title: Installation in k8s with Helm
description: How to install Falco Talon in Kubernetes with Helm
categories: [Examples]
tags: [test, sample, docs]
---

## Helm

The helm chart is available on the official [`falcosecurity/charts repository`](https://github.com/falcosecurity/charts/tree/master/charts/falco-talon).
Two main config files are provided:
* `values.yaml` allows you to configure the static settings of `Falcon Talon` and its deployment
* `rules.yaml` contains the rules to set

{{% alert title="Info" color="info" %}}
If your `values.yaml` contains `watchRules: true`, the changes in the rules are detected and the `Falco Talon` pods will automatically reload their configuration.
{{% /alert %}}

### Install

To install Falco Talon, first add the chart repository:
```shell
helm repo add falcosecurity https://falcosecurity.github.io/charts
```

In case you already have the remote repository configured, updated it:
```bash 
helm repo update falcosecurity
```

Now, just deploy **falcosecurity/falco-talon** chart:

```shell
helm upgrade --install falco-talon falcosecurity/falco-talon
```

After deploying, you can check if pods are running properly:
```bash
kubectl get pods -n <namespace> | grep falco-talon
```
