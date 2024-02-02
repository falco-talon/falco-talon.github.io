---
title: Installation in k8s with Helm
description: How to install Falco Talon in Kubernetes with Helm
categories: [Examples]
tags: [test, sample, docs]
---

## Helm

The helm chart is available in this folder [`deployment/helm`](https://github.com/Issif/falco-talon/tree/main/deployment/helm).
Two config files are provided:
* `values.yaml` allows you to configure `Falcon Talon` and the deployment
* `rules.yaml` contains rules to set

## Clone and install

Git clone is used to target and create a copy of the ```falco-talon``` repository:

```shell
gh repo clone Falco-Talon/falco-talon
```

Once downloaded, change directory to the Helm folder before running the ```helm install``` command:

```shell
cd falco-talon/deployment/helm/
helm install falco-talon . -n falco --create-namespace
```
