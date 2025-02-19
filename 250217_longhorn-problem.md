## LONGHORN UNINSTALLING PROBLEM

### Check jobs

```
kubectl get jobs -n longhorn-system
```

### If job fails 

```
kubectl logs -n longhorn-system -l job-name=longhorn-uninstall
```

"deleting-confirmation-flag" may be problem for example.

```
...
level=fatal msg="cannot uninstall Longhorn because deleting-confirmation-flag is set to `false`. 
...
```

```
kubectl patch settings.longhorn.io deleting-confirmation-flag -n longhorn-system --type='merge' -p '{"value": "true"}'
```

## Additional notes

### Delete ns forcibly (if --force don't work)

```
kubectl get ns longhorn-system -o json | jq '.spec.finalizers = []' | kubectl replace --raw "/api/v1/namespaces/longhorn-system/finalize" -f -
```

### Delete pv/pvc forcibly

```
kubectl patch pvc {PVC_NAME} -p '{"metadata":{"finalizers":null}}'
```


