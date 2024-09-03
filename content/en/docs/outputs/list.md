---
title: List of Outputs
weight: 5
description: >
  Available outputs
---

The name of the output has to be used as value for the `target` field of the `output` section of the `action`.

## `local:file`

* Name: `file`
* Category: `local`
* Description: **Store on local filesystem**

### Parameters

* `destination`: (required) Destination folder for the file

### Example

```yaml
- action: Get logs of the pod
  actionner: kubernetes:download
  parameters:
    tail_lines: 200
  output:
    target: local:file
    parameters:
      destination: /var/logs/falco-talon/
```

## `aws:s3`

* Name: `s3`
* Category: `aws`
* Description: **Store on AWS S3**

### Parameters

* `bucket`: (required) Destination bucket
* `prefix`: Prefix for the key
* `region`: Region of the bucket

### Permissions

```json
{
  "Id": "Policy1724925555994",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1724925537082",
      "Action": [
        "s3:PutObject",
        "s3:PutObjectAcl"
      ],
      "Effect": "Allow",
      "Resource": [
            "arn:aws:s3:::${BucketName}/",
            "arn:aws:s3:::${BucketName}/*"
      ]
    }
  ]
}
```

### Example

```yaml
- action: Get logs of the pod
  actionner: kubernetes:download
  parameters:
    tail_lines: 200
  output:
    target: aws:s3
    parameters:
      bucket: falco-talon
      prefix: files
      region: eu-west-1
```

## `minio:s3`

* Name: `s3`
* Category: `minio`
* Description: **Store on Minio**

### Parameters

* `bucket`: (required) Destination bucket
* `prefix`: Prefix for the key

### Example

```yaml
- action: Get logs of the pod
  actionner: kubernetes:download
  parameters:
    tail_lines: 200
  output:
    target: minio:s3
    parameters:
      bucket: falco-talon
      prefix: /files
``` 