# Pods

## Pod Configuration

Example of a pod configuration file :


```

apiVersion: v1

kind: Pod

metadata:
  name: pode-name
  
spec:
  containers:
  - name: container1-name
    image: container1-image
  - name: container2-name
    image: container2-image

```


We can break down the configuration of a pod into four main components:

  - **apiVersion** : Version of the Kubernetes API we are going to use.
  - **kind** : The kind of Kubernetes object we are trying to create, which is a Pod in
    this case.
  - **metadata** : Metadata or information that uniquely identifies the object
    we're creating.
  - **spec** : Specification of our pod, such as container name, image name, volumes,
    and resource requests.
