## Prerequisites

- [Spark Operator](../README.md)
- [ServiceAccount, ClusterRole and ClusterRoleBinding](../01-test-basic-app/README.md)


### Create Storageclass



### Create pv


### Create pvc 


### Deploy Spark History Server

<!-- - [Longhorn](https://github.com/ElifSinemAktas/experimental-devops-project/blob/main/deployment/docs/02_deploy_longhorn.md) -->
- 

<!-- ### Create PVC

Use pvc.yaml and create pvc with the following command

```bash
kubectl apply -f pvc.yaml
```

Check persistentVolumeClaim

```bash
kubectl get pvc
```

Output:

```
NAME             STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
spark-logs-pvc   Bound    pvc-29367f86-3318-4a44-8f0b-222ddc3af9d4   1Gi        RWO            longhorn       <unset>                 4s
```

Check persistentVolume
```bash
kubectl get pv
```

Output:
```
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pvc-29367f86-3318-4a44-8f0b-222ddc3af9d4   1Gi        RWO            Delete           Bound    default/spark-logs-pvc   longhorn       <unset>                          97s
``` -->
