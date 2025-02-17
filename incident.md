## LONGHORN UNINSTALLING PROBLEM

### Delete ns forcibly (if --force don't work)

```
kubectl get ns longhorn-system -o json | jq '.spec.finalizers = []' | kubectl replace --raw "/api/v1/namespaces/longhorn-system/finalize" -f -
```
### Delete pv/pvc forcibly

```
kubectl patch pvc {PVC_NAME} -p '{"metadata":{"finalizers":null}}'
```