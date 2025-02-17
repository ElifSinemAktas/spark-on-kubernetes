# Optimizing Apache Spark on Kubernetes

## 1. Spark Application Configurations

### 1.1. Resource Allocation
```yaml
spark.executor.memory=4g
spark.executor.cores=4
spark.driver.memory=2g
spark.dynamicAllocation.enabled=true
spark.dynamicAllocation.minExecutors=2
spark.dynamicAllocation.maxExecutors=10
```

---

## 2. Kubernetes-Specific Configurations

### 2.1. Resource Requests & Limits
```yaml
spark.kubernetes.executor.request.cores=2
spark.kubernetes.executor.limit.cores=4
spark.kubernetes.executor.request.memory=4g
spark.kubernetes.executor.limit.memory=6g
```

### 2.2. Node Selectors & Affinity

```yaml
spark.kubernetes.node.selector.spark=true
```

Label your nodes:
```bash
kubectl label node <your-node-name> spark=true
```

### 2.3. Tolerations for Tainted Nodes

```yaml
spark.kubernetes.tolerations="[{'key': 'spark', 'operator': 'Exists'}]"
```

## 3. Storage & Data Optimization

### 3.1. External Shuffle Service Alternative
```yaml
spark.shuffle.service.enabled=false
```

### 3.2. Optimize Shuffle Performance
```yaml
spark.sql.shuffle.partitions=200
```

---

## 4. Performance Tuning

### 4.1. Optimize Serialization
```yaml
spark.serializer=org.apache.spark.serializer.KryoSerializer
```

### 4.2. Enable Speculative Execution
```yaml
spark.speculation=true
```

### 4.3. Optimize Garbage Collection
For Java 8+:
```yaml
spark.executor.extraJavaOptions="-XX:+UseG1GC"
```
For Java 11+:
```yaml
spark.executor.extraJavaOptions="-XX:+UseZGC"
```

---

## 5. Networking & Security

### 5.1. Enable Secure Authentication
```yaml
spark.hadoop.security.authentication=kerberos
spark.hadoop.security.authorization=true
```

### 5.2. Reduce Network Overhead
```yaml
spark.hadoop.fs.s3a.fast.upload=true
```

---

## 6. Logging & Monitoring

### 6.1. Enable Spark Event Logging
```yaml
spark.eventLog.enabled=true
spark.eventLog.dir=s3a://spark-logs/
```

### 6.2. Enable Prometheus Metrics
```yaml
spark.metrics.conf.driver.sink.prometheusServlet.class=org.apache.spark.metrics.sink.PrometheusServlet
spark.metrics.conf.executor.sink.prometheusServlet.class=org.apache.spark.metrics.sink.PrometheusServlet
```

---

## 7. Fault Tolerance & Reliability

### 7.1. Auto-Retry Failed Jobs
```yaml
spark.task.maxFailures=4
```

### 7.2. Graceful Decommissioning of Executors
```yaml
spark.dynamicAllocation.shuffleTracking.enabled=true
spark.dynamicAllocation.executorIdleTimeout=60s
```

---

## 8. Spark Operator Configuration

```yaml
apiVersion: sparkoperator.k8s.io/v1beta2
kind: SparkApplication
metadata:
  name: my-spark-app
  namespace: spark
spec:
  type: Scala
  mode: cluster
  image: "my-spark-image:latest"
  imagePullPolicy: Always
  mainClass: "com.example.MySparkJob"
  mainApplicationFile: "local:///opt/spark/jars/my-spark-app.jar"
  
  sparkConf:
    "spark.dynamicAllocation.enabled": "true"
    "spark.dynamicAllocation.minExecutors": "2"
    "spark.dynamicAllocation.maxExecutors": "10"
    "spark.executor.memory": "4g"
    "spark.executor.cores": "4"
    "spark.driver.memory": "2g"
    "spark.driver.cores": "2"
    "spark.sql.shuffle.partitions": "200"
    "spark.serializer": "org.apache.spark.serializer.KryoSerializer"
    "spark.speculation": "true"
    "spark.eventLog.enabled": "true"
    "spark.eventLog.dir": "s3a://spark-logs/"
    "spark.kubernetes.allocation.batch.size": "5"

  driver:
    cores: 2
    coreLimit: "2500m"
    memory: "2g"
    labels:
      version: "3.4.1"
    serviceAccount: "spark"

  executor:
    instances: 2
    cores: 4
    coreLimit: "4500m"
    memory: "4g"
    labels:
      version: "3.4.1"
```

Apply the SparkApplication:
```bash
kubectl apply -f my-spark-app.yaml
```
Monitor logs:
```bash
kubectl logs -f spark-<app-name>-driver -n spark
```
Monitor executors:
```bash
kubectl get pods -n spark
```

