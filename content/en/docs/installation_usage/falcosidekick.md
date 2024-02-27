---
title: Connect Falcosidekick to Falco Talon
description: How to receive the Falco Events from Falcosidekick
categories: [Examples]
tags: [test, sample, docs]
---

Once you have installed `Falco Talon` with Helm in the `falco` namespace, you have to connect `Falcosidekick` by adding the flag `--set falcosidekick.config.webhook.address=http://falco-talon:2803`
```shell
helm install falco falcosecurity/falco --namespace falco \
  --create-namespace \
  --set tty=true \
  --set falcosidekick.enabled=true \
  --set falcosidekick.config.webhook.address=http://falco-talon:2803
```

{{% alert title="Warning" color="warning" %}}
`Falcosidekick` doesn't provide a dedicated output for `Falco Talon` yet, but it will happen to make easier the connection soon.
{{% /alert %}}
