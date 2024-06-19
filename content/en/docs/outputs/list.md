---
title: List of Outputs
weight: 5
description: >
  Available outputs
---

The name of the output has to be used as value for the `target` field of the `output` section of the `action`.

### `local:file`

* Description: **Store on local filesystem**
* Parameters:
  * `destination`: (required) Destination folder for the file

### `aws:s3`

* Description: **Store on AWS S3**
* Parameters:
  * `bucket`: (required) Destination bucket
  * `prefix`: Prefix for the key
  * `region`: Region of the bucket

### `minio:s3`

* Description: **Store on Minio**
* Parameters:
  * `bucket`: (required) Destination bucket
  * `prefix`: Prefix for the key

