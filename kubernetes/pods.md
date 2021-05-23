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

## Create a pod with  resource requirements

```


apiVersion: v1
kind: Pod
metadata:
  name: resource-requirements-pod
spec:
  containers:
    - name: container-with-resource-requirements
      image: nginx
      resources:
        limits:
          memory: "128M"
          cpu: "1"
        requests:
          memory: "64M"
          cpu: "0.5"

```

In this YAML file, we define the minimum memory requirement for the container
to be 64 MB and the maximum memory that the container can occupy to be
128 MB. If the container tries to allocate more than 128 MB of memory, it will be
killed with a status of OOMKilled .

The minimum CPU requirement for CPU is 0.5 (which can also be understood as
500 milli-CPUs and can be written as 500m instead of 0.5 ) and the container will
only be allowed to use a maximum of 1 CPU unit.


## Lifecle of a Pod

**Pending** : This means that the pod has been submitted to the cluster, but the
controller hasn't created all its containers yet. It may be downloading images or
waiting for the pod to be scheduled on one of the cluster nodes.


**Running** :  This state means that the pod has been assigned to one of the cluster
nodes and at least one of the containers is either running or is in the process of
starting up.


**Succeeded** : This state means that the pod has run, and all of its containers
have been terminated with success.


**Failed** : This state means the pod has run and at least one of the containers
has terminated with a non-zero exit code, that is, it has failed to execute
its commands.


**Unknown** : This means that the state of the pod could not be found. This may be
because of the inability of the controller to connect with the node that the pod
was assigned to.


## Probes / Health Checks


A probe is a health check that can be configured to check the health of the containers
running in a pod. A probe can be used to determine whether a container is running or
ready to receive requests. A probe may return the following results:

- **Success** : The container passed the health check.
- **Failure** : The container failed the health check.
- **Unknown** : The health check failed for unknown reasons.