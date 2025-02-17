## Architecture

![image](./images/architecture-diagram.png)


## Summary:

Spark Operator is a Kubernetes Controller designed to manage and run Spark applications.

- A user uses the sparkctl (or kubectl) to create a SparkApplication object. 

- The SparkApplication controller receives the object through a watcher from the API server, creates a submission carrying the spark-submit arguments, and sends the submission to the submission runner. 

- The submission runner submits the application to run and creates the driver pod of the application. Upon starting, the driver pod creates the executor pods. 

- While the application is running, the Spark pod monitor watches the pods of the application and sends status updates of the pods back to the controller, which then updates the status of the application accordingly.
    
- Webhook?

## Deploy Spark Operator (Kubeflow)

Add spark-operator helm repository
```bash
helm repo add spark-operator https://kubeflow.github.io/spark-operator
helm repo update
```

Install/deploy spark-operator

```bash
helm install spark-operator spark-operator/spark-operator \
  --namespace spark-operator \
  --create-namespace \
  --set sparkJobNamespace=default \
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
...
scheduledsparkapplications.sparkoperator.k8s.io       2025-01-29T08:21:33Z
sparkapplications.sparkoperator.k8s.io                2025-01-29T08:21:33Z
```

Check the all resources of spark-operator
```bash
kubectl get all -n spark-operator
```

## Resources:

- User guide: https://www.kubeflow.org/docs/components/spark-operator/user-guide/

- Architecture: https://www.kubeflow.org/docs/components/spark-operator/overview/#architecture

- Writing a spark application: https://www.kubeflow.org/docs/components/spark-operator/user-guide/writing-sparkapplication/

- Kubeflow helm chart : https://github.com/kubeflow/spark-operator/blob/master/charts/spark-operator-chart/values.yaml

- Spark-submit VS Spark-operator: https://spot.io/blog/setting-up-managing-monitoring-spark-on-kubernetes/


## Questions

- Keep log and view with history server
- Store application files (.jar, .py etc) ?
- Automate deletion of driver ?
