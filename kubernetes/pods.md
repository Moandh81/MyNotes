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
    
   
   
   ### Example :
   
   We will create a single file pod1.yaml with the following content :
   
   
   ```
apiVersion: v1
kind: Pod
metadata:
  name: first-pod

spec:
  containers:
  - name: my-first-container
    image: nginx
   
   ```

We will run the following command in the terminal in order to create a pod
with the preceding configuration :

   ```
kubectl create -f pod1.yaml
   ```

## Create a pod in a different namespace


Kubernetes supports namespaces to create multiple virtual clusters within the same
physical cluster. 

We may need to use namespaces if we want to provide separate
environments to our different teams working on the same cluster.

Namespaces also help in scoping the object names. For example, you cannot have two pods with the
same name within the same namespace.

However, it's possible to have two pods with
the same name in two different namespaces.



### Get the list of the namespaces

```

kubectl get ns

```

### Create a pod in a different namespce from the CLI

```

kubectl --namespace kube-public create -f pod1.yaml

```

### Create a pod in a different namespce from the configuration file

```

apiVersion: v1
kind: Pod
metadata:
  name: first-pod
  namespace: kube-public

spec:
  containers:
  - name: my-first-container
    image: nginx

```

## Change the namespace for all subsequent kubectl commands :

```
kubectl config set-context $(kubectl config current-context) --namespace kube-public

```

## Define a pod that runs a container with a command :


```

apiVersion: v1
kind: Pod
metadata:
  name: command-pod
spec:
  containers:
  - name: container-with-command
    image: ubuntu
    command:
    - /bin/bash
    - -ec
    - while :; do echo '.'; sleep 5; done

```


## Define a port that exposes a port :


```

apiVersion: v1
kind: Pod
metadata:
  name: port-exposed-pod
spec:
  containers:
    - name: container-with-exposed-port
      image: nginx
      ports:
        - containerPort: 80

```
