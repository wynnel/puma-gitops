Secret for image credentials:
```bash
./makeImageCreds.sh -namespace=test -dest=../releases/test/image-creds.yaml -password=<password>
```

Secret for license:
```bash
./makeLicense.sh -namespace=test -dest=../releases/test/gateway-license.yaml -license=<license.xml file>
```

Example Secret from values file (env.yml):
```bash
kubectl create secret generic env --dry-run  -n test  -o yaml --from-file=env.yaml  | kubeseal --format yaml > "../releases/test/env.yaml"
```
```helmyaml
  valuesFrom:
  - secretKeyRef:
      name: env
      key: env.yaml
```
