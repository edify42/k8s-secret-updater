# SecretUpdater

## Purpose

This app acts as the glue between Confidant and Kubernetes.

Also manages AWS SSM Parameter store for type String and SecureString.

It receives a webhook from Confidant upon changes to credentials or services, and pulls the appropriate secrets out of Confidant and places them into Kubernetes/AWS as Secrets within a namespace or AWS SSM Parameter store parameters.

## Prerequisites

### K8s Cluster Availability/Access

Secret updater needs access to the k8s cluster in question (api-server) with a type of login (username/password)

### AWS Access

Relevant IAM role setup or Account keys with access.

## Configuration

### Confidant

You may specify `secret-name` in a credential's metadata section, to have the Secret name custom defined to be the value you select.

Use the `secret-case` metadata to set the secret key to UPPER CASE.

This is useful if your helm chart defines the expected secret name based on the chart name, but you have several apps using the chart, or different environments needing different Credential values in the same-named Secret.

### Kubernetes

You may set an annotation on a Deployment, to avoid the automatic rolling update that this app defaults to triggering.

The annotation goes in `metadata.annotations`, and needs the be called `secretupdater.ffx.io/skip_reload`, with any non-false value.

Services starting with `k8s-` will be mapped to k8s namespaces within the selected cluster.

### AWS

A typical AWS setup will have the secret updater app running in your AWS account. It could be running on an EC2 node or on Fargate etc - it will have whatever role/permission of the underlying infrastructure.

This is the **default** Confidant AWS type service with a prefix `aws-account-default`. Any AWS SSM Parameter store variables should be defined as Confidant credentials and assigned to this service.

You can add additional AWS accounts by creating more Confidant services and adding the relevant `aws_access_key_id` and `aws_secret_access_key` values with an `aws_region`
