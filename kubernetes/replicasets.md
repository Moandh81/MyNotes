# Repilicasets

ReplicaSet acts as a supervisor for multiple Pods across the different nodes in a
Kubernetes cluster. 

A ReplicaSet will terminate or start new Pods to match the configuration specified in the ReplicaSet template.

For this reason, it is a good idea to
use them even if your application only needs one Pod. 

Even if someone deletes the
only running Pod, the ReplicaSet will ensure that a new Pod is created to replace it,
thereby ensuring that one Pod is always running.

## Replicaset Configuration

```

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      environment: production
  template:
    metadata:
      labels:
        environment: production
    spec:
      containers:
      - name: nginx-container
        image: nginx


```