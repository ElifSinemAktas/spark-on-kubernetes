## Prerequisites

- [Spark Operator](../README.md)

## Create service account, role and rolebinding

Create service account where your pods will run. 
(We defined this serviceaccount in SparkApplication (yaml file) driver configuration)

```bash
kubectl create serviceaccount spark --namespace default
```

### Role/Rolebinding-Option-1
```bash
kubectl create clusterrolebinding spark-role-binding --clusterrole=edit --serviceaccount=default:spark --namespace=default
```
### Role/Rolebinding-Option-2

Create role with permissions
```bash
kubectl apply -f spark-role.yaml
```

Bind this role to 
```bash
kubectl apply -f spark-rolebinding.yaml
```

## Deploy/Monitor/Delete application

```bash
kubectl apply -f spark-pi.yaml
```

Check the application
```bash
kubectl get sparkapplications 
```

Delete the job (automate it?)

```bash
kubectl delete -f spark-pi.yaml
```

If failed check controllers logs
```bash
kubectl logs spark-operator-controller-7d9dd879-djddw -n spark-operator
```


