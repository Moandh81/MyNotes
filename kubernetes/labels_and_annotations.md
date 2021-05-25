# Labels

Labels are the metadata that contain identifiable information pertaining to the
Kubernetes objects.

These are basically key-value pairs that can be attached to objects such as pods. Each key must be unique for an object.
 
Labels contain information that is meaningful to users. 

Labels can be attached to pods at the time
of creation and can also be added or modified during their runtime too.


Example:

```
metadata:
  labels:
    key1: value1
    key2: value2

```

## Why do we need labels ?

Labels are generally used for organizing a subset of objects. These objects can then
be filtered on the basis of these labels.

With labels, you can also run your specific
pods on selected nodes.


### Organizing pods by organization / team /  Project


Example :

```


metadata:
  labels:
    environment: production
    team: devops-infra
    project: test-k8s-infra

```

### Running selective pods on specific nodes


Another useful scenario can be when you want your pod to be assigned to a certain
node with specific hardware or other properties. This can be achieved by adding
labels to the nodes that have special hardware or other properties. We can use
**nodeSelector** to assign the pod to any node that has a particular label.

Consider the following example:


```

apiVersion: v1
kind: Pod
metadata:
  name: pod-with-node-selector
spec:
  containers:
  - name: first-container
    image: nginx
  nodeSelector:
    region: east-us
    disktype: ssd


```


The preceding pod template can be used to make sure the pod will be assigned to a
node that is in the east-us region and has ssd storage. This check is based on the
labels added to the nodes. So, we need to ensure that the appropriate **region** and
**disktype** labels are assigned to all nodes where applicable.



### Create a pod with labels:

Example :


```

apiVersion: v1
kind: Pod
metadata:
  name: pod-with-labels
  labels:
    app: nginx
    foo: bar
spec:
  containers:
  - name: first-container
    image: nginx

```


### Add a label to a running pod

```
kubectl label pod pod-name newkey=newvalue

```

### Add multiple labels to a running pod

```

kubectl label pod pod-name newkey1=newvalue1 newkey2=newvalue2

```

### Modify or delete existing labels on running pod


**Modify** :

```
kubectl label --overwrite pod pod-name app=nginx-application

```

** Delete**:


```
kubectl label --overwrite pod pod-name app-


```


### Select Kubernetes Objects using Label selectors

In order to group various objects based on their labels, we use a label selector.

It allows users to identify a set of objects matching certain criteria.


```

kubectl get pods -l {label-selector}

```

#### Equality based selector

environment=production

team!=devops


Similarly, we can also use both the selectors together, separated by commas ( , ):

environment=production,team!=devops-infra


To display the labels specific to a specific pod :


```

kubectl get pod pod-name --show-labels

```

#### Set based selector

Set-based selectors allow Kubernetes objects to be selected on the basis of a set of
values for given keys.

These kinds of selectors allow us to match all objects that have
a given label key with a value in a given set of values.

There are three kinds of operators: **in** , **notin** , and **exists** .

Let's see what these
operators mean with the help of some examples

```
environment in (production, staging)

```



```

team notin (devops-infra)

```


```
!critical

```
The previous example matches all the objects that don't have the **critical** key value.



```
kubectl get pods -l 'environment,role,role notin (backend)'

```



# Annotations

labels are used to add the identifying metadata that we can later use to filter or select objects by. 

However, labels have certain constraints in
terms of what we can store in the values, such as the limitation of 63 characters and
alphanumeric characters at the beginning and end. 

Annotations, on the other hand,
have fewer constraints in terms of what kind of data can be stored in them.

However, we cannot filter or select objects by using annotations.



Annotations are also key-value pairs that can be used to store the unstructured
information pertaining to the Kubernetes objects.



Example : 

```
metadata:
  annotations:
    key1: value1
    key2: value2

```


## Use case for annotations

Annotations are generally used to add metadata that won't be used to filter or select
objects. It's used to add metadata that will be used by users or tools to get more
subjective information regarding the Kubernetes objects.

- Annotations can be used to add timestamps, commit SHA, issue tracker links, or
names/information about users who are responsible for specific objects in an
organization. In this case, we can use the following type of metadata, depending
on our use case:

```

metadata:
  annotations:
    timestamp: 123456789
    commit-SHA: d6s9shb82365yg4ygd782889us28377gf6
    JIRA-issue: "https://your-jira-link.com/issue/ABC-1234"
    owner: "https://internal-link.to.website/username"


```

- Annotations can also be used to add information about client libraries or tools.
We can add information such as the name of the library, the version used, and
public documentation links. This information can later be used for debugging
issues in our application:


```

metadata:
  annotations:
    node-version: 13.1.0
    node-documentation: "https://nodejs.org/en/docs/"

```


- We can also use annotations to store the previous pod configuration deployed.
This can be really helpful in figuring out what configuration was deployed before
the current revision and what has changed:




```


metadata:
  annotations:
    previous-configuration: "{ some json containing the
previously deployed configuration of the object }"

```


## Working with annotations



We can add annotations to a running pod by using the following command :

```

kubectl annotate pod <pod_name> <annotation_key>=<annotation_label>

```

We can also modify  an annotation as follows :


```

kubectl annotate --overwrite pod <pod_name> <annotation_
key>=<annotation_label>

```

We can delete an annotation as follows :


```

kubectl annotate pod <pod_name> <annotation_key>-

```


