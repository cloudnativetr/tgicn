# Supply Chain Security Manager for Tekton Pipelines

- Hosted by [@developerguyba](https://github.com/developer-guy) and [@devenes](https://github.com/devenes)
- 03/11/2022
- You can watch the episode on [YouTube](https://youtu.be/jqy8fg0inbs/):

[![Tekton Chains](https://img.youtube.com/vi/jqy8fg0inbs/0.jpg)](https://youtu.be/jqy8fg0inbs/)

## Table of Contents

- [Tekton Chains: The Supply Chain Security Manager for your Tekton Pipelines](https://cd.foundation/blog/2022/10/18/tekton-chains/)

## Week in Review

## Show Notes

- [Tekton Catalog](https://github.com/tektoncd/catalog)
- [CNCF TAG Security](https://github.com/cncf/tag-security)
- [Tekton Chains Config](https://tekton.dev/docs/chains/config/)
- [Tekton Chains Doc](https://tekton.dev/docs/chains/)

## Demo of the Week

Install cosign and crane:

```bash
brew install cosign
```

```bash
brew install crane
```

Let’s spin up our local development cluster with kind:

```bash
kind create cluster --image=kindest/node:v1.24.0
```

Next, let’s install Tekton Pipelines. To do so, we can run the following command:

```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```

Check that all the pods are running before moving to the next steps:

```bash
kubectl get pods --namespace tekton-pipelines --watch
```

Once Tekton Pipeline is installed, the next step is to install Tekton Chains.

To install the latest version of Chains to your Kubernetes cluster, we can run the following command:

```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/chains/latest/release.yaml
```

Again, check that all the pods are in a running step before moving to the next steps:

```bash
kubectl get pods -n tekton-chains --watch
```

we will configure the configmap to use in-totoformat and oci as the storage with the help of the following commands:

```bash
kubectl patch configmap chains-config -n tekton-chains -p='{"data":{"artifacts.taskrun.format": "in-toto"}}'
kubectl patch configmap chains-config -n tekton-chains -p='{"data":{"artifacts.taskrun.storage": "oci"}}'
```

One of the schemes for generating key pairs supported in Cosign is Kubernetes Secret with the following prefix k8s:// , to do that run the simple command:

```bash
cosign generate-key-pair k8s://tekton-chains/signing-secrets
```

Search kaniko task in the catalog:

```bash
tkn hub search kaniko
```

Get kaniko task from the catalog:

```bash
tkn hub get task kaniko --version 0.6 > kaniko.yaml
```

Add a `add-dockerfile` step to the task using the following command:

```bash
awk '/steps:/ {print; print "    - name: add-dockerfile\n      workingDir: $(workspaces.source.path)\n      image: bash\n      script: |\n        set -e\n        echo \"FROM alpine@sha256:69e70a79f2d41ab5d637de98c1e0b055206ba40a8145e7bddb55ccc04e13cf8f\" | tee $(params.DOCKERFILE)"; next}1' kaniko.yaml > kaniko.yaml.tmp && mv kaniko.yaml.tmp kaniko.yaml
```

Create a kaniko taskrun:

```bash
kubectl apply -f kaniko.yaml
```

List the tasks:

```bash
tkn task list --all-namespaces
```

For the sake of simplicity and not to struggle with the authentication process, we’ll be using ttl.sh registry to publish our container image in kaniko Task.

```bash
IMAGE_NAME=tekton-chains-demo
REGISTRY=ttl.sh
```

Now, let’s run that Task:

```bash
tkn task start --param IMAGE=$REGISTRY/$IMAGE_NAME --use-param-defaults --workspace name=source,emptyDir="" kaniko
```

In order to track the TaskRun progress run:

```bash
tkn taskrun logs $(tkn taskrun list | awk '{print $1}' | tail -n 1) -f -n default
```

Check the annotations of your TaskRun, and you should be able to see the `chains.tekton.dev/signed=true` annotation.

```bash
kubectl get tr $(tkn taskrun list | awk '{print $1}' | tail -n 1) -o json | jq -r .metadata.annotations
```

Let’s double-check whether the Tekton Chains worked by looking at the given image tags because Cosign must have created at least two additional tags for the given image, `.att` for the attestation and `.sig` for its signature. We can use `crane` tool to do that check:

```bash
crane ls $REGISTRY/$IMAGE_NAME
```

Verify the signature:

```bash
cosign verify --key k8s://tekton-chains/signing-secrets $REGISTRY/$IMAGE_NAME | jq
```

Verify the attestation:

```bash
cosign verify-attestation --key k8s://tekton-chains/signing-secrets --type slsaprovenance $REGISTRY/$IMAGE_NAME | jq
```

You can pipe the output to see the attestation content 👇

```bash
cosign verify-attestation --key k8s://tekton-chains/signing-secrets --type slsaprovenance $REGISTRY/$IMAGE_NAME | jq -r '.payload | @base64d | fromjson'
```

---

Cleanup the cluster:

```bash
kind delete cluster
```
