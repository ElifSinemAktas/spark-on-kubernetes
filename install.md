
```bash
helm repo add spark-operator https://kubeflow.github.io/spark-operator
helm repo update
```

```bash
helm install spark-operator spark-operator/spark-operator `
  --namespace spark-operator `
  --create-namespace `
  --set sparkJobNamespace=default `
  --set webhook.enable=true
```

Explanation:
```
--namespace spark-operator: Installs in a separate namespace.
--set sparkJobNamespace=default: Runs Spark jobs in the default namespace.
--set webhook.enable=true: Enables the webhook for better Kubernetes integration.
```

Check the Custom Resource Definitions (CRDs) to confirm installation

```bash
kubectl get crds
```

```
scheduledsparkapplications.sparkoperator.k8s.io       2025-01-29T08:21:33Z
sparkapplications.sparkoperator.k8s.io                2025-01-29T08:21:33Z
```

Create service account where your pods will run
```bash
kubectl create serviceaccount spark --namespace default
```

```bash
kubectl apply -f spark-role.yaml
```

```bash
kubectl create clusterrolebinding spark-role-binding `
  --clusterrole=spark-role `
  --serviceaccount=default:spark
```

How spark knows this serviceaccount, is the "spark" name default?

```bash
kubectl apply -f spark-pi.yaml
```

If you need to delete
```bash
kubectl delete -f spark-pi.yaml
```



