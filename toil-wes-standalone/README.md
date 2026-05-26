# toil-wes-standalone

Standalone Helm chart to deploy a Toil WES server only with the necessary runtime patches for an S3-compatible MinIO backend.

## What this chart does

- Deploys a Toil WES pod (Deployment + Service).
- Applies runtime patches at startup on Toil AWSJobStore:
  - `public=True` -> `public=False`
  - `encryptable=True` -> `encryptable=False`
- Injects a `sitecustomize.py` to force `addressing_style=path` on the boto3/Toil side.
- Launches `toil server` with `--bypass_celery` and propagation of S3 variables to Kubernetes workers.

## Prerequisites

- A Kubernetes cluster.
- An S3-compatible endpoint (e.g., MinIO).
- S3 credentials via an existing secret (default `s3-service`) or via secret creation by the chart.

## Installation

```bash
helm upgrade --install toil-wes ./toil-wes-standalone -n zoo --create-namespace
```

## Existing MinIO Configuration

Default values:

- endpoint: `http://s3-service.zoo.svc.cluster.local:9000`
- secret credentials: `s3-service`
- secret keys: `rootUser` / `rootPassword`

## Create the secret via the chart (optional)

```yaml
s3:
  credentials:
    createSecret: true
    existingSecret: ""
    accessKeyKey: rootUser
    secretKeyKey: rootPassword
    accessKeyValue: minio-admin
    secretKeyValue: minio-secret-password
```

## WES Endpoint

The service exposes Toil WES on:

- `http://<release-name>-toil-wes-standalone:<port>/ga4gh/wes/v1/`
