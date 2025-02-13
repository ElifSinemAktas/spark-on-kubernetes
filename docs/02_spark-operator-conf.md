# Configuring Spark Operator

## 1. Modify Spark Operator Helm Values (Recommended)
If you installed the Spark Operator using Helm, you can customize its configuration by modifying the Helm values.

### Get Current Values
```sh
helm show values spark-operator/spark-operator
```

### Create a Custom Values File
Create a `values.yaml` file and override the default configurations. For example change the jobNamespaces from "default" to "my-namespace"🤗 :

```yaml
spark:
  # List of namespaces where to run spark jobs.
  # If empty string is included, all namespaces will be allowed.
  # Make sure the namespaces have already existed.
  jobNamespaces:
  - default
```

### Upgrade the Helm Installation
Apply the changes by upgrading the Spark Operator (if needed):

```sh
helm upgrade --install spark-operator spark-operator/spark-operator -n spark-operator --values values.yaml
```

### Apply Changes
After saving, restart the Spark Operator:

```sh
kubectl rollout restart deployment spark-operator -n spark-operator
```

## 2. Configure Spark Defaults in a ConfigMap
You can create a Spark configuration `ConfigMap` and mount it to the operator.

### Create a ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: spark-defaults
  namespace: spark-operator
data:
  spark-defaults.conf: |
    spark.executor.memory=4g
    spark.driver.memory=2g
    spark.kubernetes.container.image=my-custom-spark-image
```

Apply it:

```sh
kubectl apply -f spark-defaults-configmap.yaml
```

### Mount It to the Spark Operator
Modify the operator deployment to mount the ConfigMap:

```yaml
volumeMounts:
  - name: spark-config
    mountPath: /etc/spark/conf
volumes:
  - name: spark-config
    configMap:
      name: spark-defaults
```

Restart the operator:

```sh
kubectl rollout restart deployment spark-operator -n spark-operator
```
