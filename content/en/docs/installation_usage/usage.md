---
title: Usage
description: How to use Falco Talon
categories: [Examples, Placeholders]
tags: [test, docs]
weight: 3
---

Falco Talon can also be used as a CLI to help you to manage your rules by checking their validities (useful in a CI), printing the result of their merges or by listing the available actionners, outputs and notifiers.

```shell
$ falco-talon --help

Falco Talon is a Response Engine for managing threats in Kubernetes 
It enhances the solutions proposed by Falco community with a dedicated, 
no-code solution. With easy rules, you can perform actions over compromised pods.

Usage:
  falco-talon [command]

Available Commands:
  actionners  Manage the actionners
  completion  Generate the autocompletion script for the specified shell
  help        Help about any command
  notifiers   Manage the Notifiers
  outputs     Manage the Outputs
  rules       Manage Falco Talon rules
  server      Start Falco Talon server
  version     Print version of Falco Talon.

Flags:
  -c, --config string       Falco Talon Config File (default "/etc/falco-talon/config.yaml")
  -h, --help                help for falco-talon
  -r, --rules stringArray   Falco Talon Rules File (default [/etc/falco-talon/rules.yaml])

Use "falco-talon [command] --help" for more information about a command.
```

## Start the server listening the events

```shell
$ falco-talon server --help
Start Falco Talon

Usage:
  falco-talon server [flags]

Flags:
  -h, --help   help for server

Global Flags:
  -c, --config string       Falco Talon Config File (default "/etc/falco-talon/config.yaml")
  -r, --rules stringArray   Falco Talon Rules File (default [/etc/falco-talon/rules.yaml])
```

## Manage the rules

```shell
$ falco-talon rules --help

Manage the rules loaded by Falco Talon. You can print them in the stdout or check their validity.

Usage:
  falco-talon rules [command]

Available Commands:
  check       Check Falco Talon Rules file
  print       Print the loaded by Falco Talon in the stdout

Flags:
  -h, --help   help for rules

Global Flags:
  -c, --config string       Falco Talon Config File (default "/etc/falco-talon/config.yaml")
  -r, --rules stringArray   Falco Talon Rules File (default [/etc/falco-talon/rules.yaml])

Use "falco-talon rules [command] --help" for more information about a command.
```

#### Check the validity of the rules

```shell
$ falco-talon rules check --help
Check Falco Talon Rules file

Usage:
  falco-talon rules check [flags]

Flags:
  -h, --help   help for check

Global Flags:
  -c, --config string       Falco Talon Config File (default "/etc/falco-talon/config.yaml")
  -r, --rules stringArray   Falco Talon Rules File (default [/etc/falco-talon/rules.yaml])
```

##### Examples

```shell
$ falco-talon rules check -r rules.yaml -r rules_override.yaml

2024-09-03T16:03:51+02:00 INF rules result="rules file valid"
```

```shell
falco-talon rules check -r rules.yaml -r rules_override.yaml

2024-09-03T16:04:53+02:00 ERR rules error="unknown actionner" action="Tests" actionner=kubernetes:wrong rule="Test bad actionner"
2024-09-03T16:04:53+02:00 FTL rules error="invalid rules"
```

#### Print the result of the merge of the rules files

```shell
$ falco-talon rules print --help

Print the result of the merge of the rules files by Falco Talon in the stdout.

Usage:
  falco-talon rules print [flags]

Flags:
  -h, --help   help for print

Global Flags:
  -c, --config string       Falco Talon Config File (default "/etc/falco-talon/config.yaml")
  -r, --rules stringArray   Falco Talon Rules File (default [/etc/falco-talon/rules.yaml])
```

### Lists

#### List the available actionners

```shell
$ falco-talon actionners list --help

List the available Actionners.

Usage:
  falco-talon actionners list [flags]

Flags:
  -h, --help   help for list

Global Flags:
  -c, --config string       Falco Talon Config File (default "/etc/falco-talon/config.yaml")
  -r, --rules stringArray   Falco Talon Rules File (default [/etc/falco-talon/rules.yaml])
```

#### List the available outputs

```shell
$ falco-talon outputs list --help

List the available Outputs.

Usage:
  falco-talon outputs list [flags]

Flags:
  -h, --help   help for list

Global Flags:
  -c, --config string       Falco Talon Config File (default "/etc/falco-talon/config.yaml")
  -r, --rules stringArray   Falco Talon Rules File (default [/etc/falco-talon/rules.yaml])
```

#### List the available notifiers

```shell
$ falco-talon notifiers list --help

List the available Notifiers.

Usage:
  falco-talon notifiers list [flags]

Flags:
  -h, --help   help for list

Global Flags:
  -c, --config string       Falco Talon Config File (default "/etc/falco-talon/config.yaml")
  -r, --rules stringArray   Falco Talon Rules File (default [/etc/falco-talon/rules.yaml])
```