Secret for image credentials:
```bash
./makeSecret.sh -namespace=test -dest=../releases/test/image-creds.yaml -password=<password>
```

Secret for license:
```bash
./makeLicense.sh -namespace=test -dest=../releases/test/gateway-license.yaml -license=<license.xml file>
```

Example Secret from values file:
```bash
kubectl create secret generic gateway-license --dry-run  -n test  -o yaml --from-file=env.yaml  | kubeseal --format yaml > "../releases/test/env.yaml"
```
```helmyaml
  valuesFrom:
  - secretKeyRef:
      name: env.yaml
      key: env.yaml
```