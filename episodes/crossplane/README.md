![crossplane](crossplane.png)

# Introduction to Crossplane

## Description

- Hosted by [Hasan Türken](https://github.com/turkenh) & [Muvaffak Onuş](https://github.com/muvaf)

- Event Date: 24/11/2022

- You can watch the episode recorded on [YouTube](https://youtu.be/Tw2R11NfscU)

### Summary

🔮 Crossplane is an open source Kubernetes add-on that transforms your cluster into a universal control plane. Crossplane enables platform teams to assemble infrastructure from multiple vendors, and expose higher level self-service APIs for application teams to consume, without having to write any code.

### Speakers

- Hasan Türken, Principal Software Engineer at Upbound

  Hasan Türken is a software engineer specialized in Kubernetes. As a certified Kubernetes administrator (CKA) and Security specialist (CKS), he actively participated in containerization and Kubernetes transformation of distributed enterprise applications, guided teams for cloud native application development, built and automated SaaS solutions with Kubernetes controllers. After being fascinated by the power of Kubernetes Control plane and following his passion, he found himself at Upbound, the company behind Crossplane, where he is enjoying opportunities that encourage him to leverage the power of Kubernetes control plane further. He is a big fan of Golang, open source software, automating stuff and everything related to Kubernetes.

- Muvaffak Onuş, Principal Staff Software Engineer at Upbound

  Muvaffak is a maintainer in Crossplane, an incubating CNCF project, where he led several big initiatives in the last couple of years to enable control plane architectures in cloud native ecosystem. Before Crossplane, he built the SaaS backend of SAP Data Intelligence platform using Kubernetes controllers.

## Table of Contents

- [Crossplane: The cloud native control plane framework](https://crossplane.io/)

### Live Stream Recording

[![Crossplane: The cloud native control plane framework](https://img.youtube.com/vi/Tw2R11NfscU/0.jpg)](https://youtu.be/Tw2R11NfscU)

### Show Notes

- [Managed Resources Demo](https://github.com/muvaf/crossplane-demos/blob/main/2022-11-24-managed-resource.md)

- [Crossplane: The cloud native control plane framework](https://crossplane.io/)

- [Deep Dive to TerraJet Part I](https://blog.crossplane.io/deep-dive-terrajet-part-i)

- [Crossplane vs Terraform](https://blog.crossplane.io/crossplane-vs-terraform/)

- [Build Your Own Heroku with Cloud Native Stack](https://github.com/muvaf/cloud-native-heroku/)

- [Cloud Native Heroku Template](https://github.com/muvaf/cloud-native-heroku/tree/main/templates/04-crossplane)

- [Tutorial: Build Your Own Heroku With Cloud Native Stack - Muvaffak Onus, Upbound & Sidarta Aguiar de Oliveira, Grupo Boticário](https://www.youtube.com/watch?v=3Aw60VXVMu8)

  Speakers: Muvaffak Onus, Sidarta Aguiar de Oliveira

  Heroku has led the transformational PaaS experience we're all used to today and we still see companies building a variant of that experience in their specific niche. However, while the opinionated nature of Heroku made it a really sound choice for general use cases, everyone knows that they will outgrow it at one point. What if you could build your own Heroku and tailor it to your needs as you grow? With the help of the cloud native stack, we will build a Heroku experience that is fully customizable for your ever changing use cases. By combining Backstage, Crossplane and several other cloud native tools, we will build our platform that can give the Heroku experience to your users consistently while your organization’s needs grow and you accommodate the changes.

- [Extensive tutorial on how Crossplane can be used with Backstage, ArgoCD and GitHub to build your own Heroku on 27th October 2022 at KubeCon NA 2022.](https://github.com/muvaf/crossplane-demos/blob/main/2022-10-24-composition.md)

- [Crossplane Design Documents](https://github.com/crossplane/crossplane/tree/master/design)

### Community Links

- [Cloud Native Türkiye Kommunity Group](https://kommunity.com/cloud-native-turkiye-kubernetes/events/introduction-to-crossplane-muvaffak-onus-hasan-turken-9b18edf7)
- [Cloud Native Türkiye Meetup Group](https://www.meetup.com/cloud-native-turkiye-kubernetes/events/289326876/)
- [Cloud Native Türkiye LinkedIn Group](https://www.linkedin.com/video/event/urn:li:ugcPost:6990411438687154176/)

# Managed Resources Demo

> This is the demo [Muvaffak Onuş](https://github.com/muvaf) did for [Cloud Native Türkiye & Kubernetes](https://linktr.ee/cloudnative) community on 24th November 2022.
> You can find the recording of the demo [here](https://www.youtube.com/watch?v=Tw2R11NfscU).

Managed Resource (MR) is Crossplane’s representation of a resource in an
external system - most commonly a cloud provider. Managed Resources are
opinionated, Crossplane Resource Model (XRM) compliant Kubernetes Custom
Resources that are installed by a Crossplane provider. Read more about
it [here](https://crossplane.io/docs/v1.10/concepts/managed-resources.html)

## Pre-requisites

- `kubectl`: https://kubernetes.io/docs/tasks/tools/

- `kind`: https://kind.sigs.k8s.io/docs/user/quick-start/

- `helm`: https://helm.sh/docs/intro/install/

- A Google Cloud Platform account

  - `gcloud`: https://cloud.google.com/sdk/docs/install

## Installation

Create a cluster.

```bash
kind create cluster --wait 5m
```

Install Crossplane.

```bash
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update

helm install crossplane --namespace crossplane-system --create-namespace --wait crossplane-stable/crossplane
```

Install the GCP provider.

```yaml
# cat <<EOF | kubectl apply -f - (then Shift+Enter, paste the content, Shift+Enter, EOF, Enter)
apiVersion: pkg.crossplane.io/v1
kind: Provider
metadata:
  name: provider-gcp
spec:
  package: xpkg.upbound.io/upbound/provider-gcp:v0.19.0
```

We need to create a GCP service account that can provision a GCP bucket.

Export the name of your GCP project ID.

```bash
# This is project ID which may be different from project name.
export GCP_PROJECT_ID="crossplane-playground"
```

Create a permission file called `permissions.yaml` with the following content.

```yaml
# cat <<EOF > permissions.yaml (then Shift+Enter, paste the content, Shift+Enter, EOF, Enter)
title: "Bucket"
description: "Permissions necessary to create a bucket."
stage: "ALPHA"
includedPermissions:
  - storage.buckets.create
  - storage.buckets.delete
  - storage.buckets.get
  - storage.buckets.getIamPolicy
  - storage.buckets.setIamPolicy
  - storage.buckets.update
  - storage.objects.list
  - storage.objects.delete
```

Create a role with these permissions.

```bash
gcloud iam roles create cloud_native_tr_bucket --project="${GCP_PROJECT_ID}" \
  --file=permissions.yaml
```

Create a Service Account.

```bash
gcloud iam service-accounts create cloud-native-tr-sa \
  --description="To be used in Cloud Native TR event" \
  --display-name="Cloud Native TR SA"
```

Bind the role to our Service Account.

```bash
gcloud projects add-iam-policy-binding "${GCP_PROJECT_ID}" \
  --member="serviceAccount:cloud-native-tr-sa@${GCP_PROJECT_ID}.iam.gserviceaccount.com" \
  --role="projects/${GCP_PROJECT_ID}/roles/cloud_native_tr_bucket"
```

Get the key for our Service Account.

```bash
gcloud iam service-accounts keys create /tmp/creds.json \
  --iam-account="cloud-native-tr-sa@${GCP_PROJECT_ID}.iam.gserviceaccount.com"
```

Create a Kubernetes `Secret` containing the key.

```bash
kubectl create secret generic gcp-creds \
  --from-file=creds=/tmp/creds.json
```

Finally, create a default `ProviderConfig` for our GCP provider to know about
this `Secret`.

```yaml
# cat <<EOF | kubectl apply -f - (then Shift+Enter, paste the content, Shift+Enter, EOF, Enter)
apiVersion: gcp.upbound.io/v1beta1
kind: ProviderConfig
metadata:
  name: default
spec:
  projectID: crossplane-playground
  credentials:
    source: Secret
    secretRef:
      namespace: default
      name: gcp-creds
      key: creds
```

## Create a Bucket

Create a `Bucket` resource.

```yaml
# cat <<EOF | kubectl apply -f - (then Shift+Enter, paste the content, Shift+Enter, EOF, Enter)
apiVersion: storage.gcp.upbound.io/v1beta1
kind: Bucket
metadata:
  name: cloud-native-tr
spec:
  forProvider:
    location: US
    storageClass: MULTI_REGIONAL
```

Wait for it to become ready.

```bash
kubectl get bucket cloud-native-tr -o yaml -w
```

Go to GCP console to check!

## Cleanup

Delete the `Bucket` resource.

```bash
kubectl delete bucket cloud-native-tr
```

Delete the GCP service account, the role and the key we created in GCP.

```bash
gcloud iam service-accounts delete "cloud-native-tr-sa@${GCP_PROJECT_ID}.iam.gserviceaccount.com"cloud-native-tr-sa
gcloud iam roles delete --project ${GCP_PROJECT_ID} cloud_native_tr_bucket
```
