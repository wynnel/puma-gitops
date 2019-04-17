# Managing Gateway Depolyments the GitOps way

Uses weaveworks' flux

### Re-create secrets for your cluster

Install sealed secrets:
```bash
helm install --namespace kube-system --name sealed-secrets-controller stable/sealed-secrets

```

Install kubeseal:
```bash
GOOS=$(go env GOOS)
GOARCH=$(go env GOARCH)
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/$release/kubeseal-$GOOS-$GOARCH
sudo install -m 755 kubeseal-$GOOS-$GOARCH /usr/local/bin/kubeseal

```

Re-create secrets by running scripts in the scripts folder.

### Configure images:
In each of the HelmRelease yaml files (ie: releases/dev/gateay.yaml), set the images to use:
```helmyaml
container:
      image: <your repository>/repository/docker-hosted/gateway
      tag: '<your image tag>'
```

### Install Weave Flux

Weave Flux [Installation instructions](https://github.com/weaveworks/flux/blob/master/chart/flux/README.md#to-install-flux-with-the-helm-operator). Set git.url to this repo.


Find the SSH public key with:

```bash
kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2
```

Give flux access to sync your cluster state with Git.

On your repository, go to _Setting > Deploy keys_ click on _Add deploy key_, check 
_Allow write access_, paste the Flux public key and click _Add key_.

Flux logs:
```bash
kubectl -n flux logs deployment/flux -f
```

By default, flux pulls from the git repo every 5 minutes, be patient!

Fluxctl: [Installation instructions](https://github.com/weaveworks/flux/blob/master/site/fluxctl.md)

```bash
fluxctl list-workloads --k8s-fwd-ns flux --all-namespaces
fluxctl list-images --k8s-fwd-ns flux --all-namespaces
```

### Remove Weave Flux
```bash
helm del --purge flux
kubectl delete customresourcedefinitions fluxhelmreleases.helm.integrations.flux.weave.works helmreleases.flux.weave.works
```

### Misc

