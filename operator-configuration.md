# Configuring Spark Operator

## 1. Modify Spark Operator Helm Values (Recommended)
If you installed the Spark Operator using Helm, you can customize its configuration by modifying the Helm values.

### Step 1: Get Current Values
```sh
helm show values spark-operator/spark-operator
```

### Step 2: Create a Custom Values File
Create a `values.yaml` file and override the default configurations. For example:

```yaml
sparkJobNamespace: "default"  # Change the default namespace for Spark jobs
enableWebhook: true           # Enable/disable the admission webhook
enableMetrics: true           # Enable Prometheus metrics
enableBatchScheduler: true    # Enable batch scheduler support
```

### Step 3: Upgrade the Helm Installation
Apply the changes by upgrading the Spark Operator:

```sh
helm upgrade --install spark-operator spark-operator/spark-operator -n spark-operator --values values.yaml
```

---

## 2. Modify the Spark Operator Deployment Directly
If you installed the operator using `kubectl apply -f <yaml>`, you can edit the deployment manually.

### Step 1: Edit the Deployment
```sh
kubectl edit deployment spark-operator -n spark-operator
```

### Step 2: Modify the Arguments
Look for the `args:` section and modify the required settings. Example:

```yaml
args:
  - "--log-level=debug"
  - "--enable-metrics=true"
  - "--enable-webhook=true"
  - "--webhook-port=8080"
```

### Step 3: Apply Changes
After saving, restart the Spark Operator:

```sh
kubectl rollout restart deployment spark-operator -n spark-operator
```

---

## 3. Configure Spark Defaults in a ConfigMap
You can create a Spark configuration `ConfigMap` and mount it to the operator.

### Step 1: Create a ConfigMap
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

### Step 2: Mount It to the Spark Operator
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

---

## Next Steps
- Use Helm values for persistent changes.
- If necessary, modify Spark application manifests to reference the new configurations.