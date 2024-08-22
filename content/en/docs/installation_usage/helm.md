---
title: Installation in k8s with Helm
description: How to install Falco Talon in Kubernetes with Helm
categories: [Examples]
tags: [test, sample, docs]
---

## Helm

The helm chart is available in this folder [`deployment/helm`](https://github.com/Issif/falco-talon/tree/main/deployment/helm).
Two main config files are provided:
* `values.yaml` allows you to configure the static settings of `Falcon Talon` and its deployment
* `rules.yaml` contains the rules to set

{{% alert title="Info" color="info" %}}
If your `values.yaml` contains `watchRules: true`, the changes in the rules are detected and the `Falco Talon` pods will automatically reload their configuration.
{{% /alert %}}

### Clone and install

Git clone is used to target and create a copy of the `falco-talon` repository:

```shell
git clone https://github.com/Falco-Talon/falco-talon.git
```

Once downloaded, change directory to the Helm folder before running the `helm install` command:

```shell
cd falco-talon/deployment/helm/
helm install falco-talon . -n falco --create-namespace
```

{{% alert title="Warning" color="warning" %}}
When the project will be more stable and a first release officially out, the helm chart will be available on https://artifacthub.io/
{{% /alert %}}
