# Managing Gateway Depolyments the GitOps way

Uses weaveworks' flux

### Install Weave Flux

Add the Weave Flux chart repo:

```bash
helm repo add weaveworks https://weaveworks.github.io/flux
```

Install Weave Flux and its Helm Operator by specifying git repo URL: 
```bash
kubectl apply -f https://raw.githubusercontent.com/weaveworks/flux/master/deploy-helm/flux-helm-release-crd.yaml
```

```bash
helm install --name flux \
--set rbac.create=true \
--set helmOperator.create=true \
--set git.url=git@github.com:wynnel/puma-gitops \
--namespace flux \
weaveworks/flux
```

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

Fluxctl:

```bash
brew install fluxctl

fluxctl list-workloads --k8s-fwd-ns flux --all-namespaces
fluxctl list-images --k8s-fwd-ns flux --all-namespaces
```

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
kubectl create secret generic gw-license --dry-run --from-file=license.xml -n test  -o yaml | kubeseal --format yaml > secrets/gateway-license.yaml

```

Re-create secrets by running scripts in the scripts folder.

### Misc

