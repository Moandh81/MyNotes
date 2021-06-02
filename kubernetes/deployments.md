# Deployments

A Deployment is a Kubernetes object that acts as a wrapper around a ReplicaSet
and makes it easier to use.

In general, in order to manage replicated services, it's recommended that you use Deployments that, in turn, manage the ReplicaSet and
the Pods created by the ReplicaSet.


The major motivation for using a Deployment is that **it maintains a history of
revisions**.

Every time a change is made to the ReplicaSet or the underlying Pods,
a new revision of the ReplicaSet is recorded by the Deployment. 

This way, using a
Deployment makes it easy to roll back to a previous state or version.


## Deployment Configuration


```


apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  selector:
    matchLabels:
      app: nginx
      environment: production
  template:
    metadata:
      labels:
        app: nginx
        environment: production

    spec:
      containers:
      - name: nginx-container
        image: nginx


```

## Strategy

In the **strategy** field we can specify which strategy the deployment shoudl replace when it replace the pods with the new ones.

This can be either **RollingUpdate** or **Recreate**.
The default value is **RollingUpdate**

### RollingUpdate


This is a strategy used to update a Deployment without having any downtime. With
the RollingUpdate strategy, the controller updates the Pods one by one.

Hence,
at any given time, there will always be some Pods 
running.


This strategy is particularly
helpful when you want to update the Pod template without incurring any downtime
for your application. However, be aware that having a rolling update means that there
may be two different versions of Pods (old and new) running at the same time.



Here's an example configuration for setting the strategy to RollingUpdate :

```

strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1
    maxSurge: 1


```

**maxUnavailable** is the maximum number of Pods that can be unavailable during
the update.

 This field can be specified as either an integer representing the maximum
number of unavailable Pods or a string representing the percentage of total replicas
that can be unavailable.

 For the preceding example configuration, Kubernetes will
ensure that no more than one replica becomes unavailable while applying an update.

The default value for maxUnavailable is 25% .

**maxSurge** is the maximum number of Pods that can be scheduled/created above
the desired number of Pods (as specified in the replicas field).

This field can also
be specified as either an integer or a percentage string, as with maxUnavailable .

The default value for maxSurge is also 25%


Hence, in the preceding example, we are telling the Kubernetes controller to update
the Pods one at a time, in such a way that no more than one Pod is ever unavailable
and that no more than four Pods are ever scheduled.


### Recreate


In this strategy, all the existing pods are killed before creating the new Pods with an
updated configuration. This means there will be some downtime during the update.

This, however, ensures that all the Pods running in the Deployment will be on the
same version (old or new).



## Rolling  back a deployment

We can use the **kubectl rollout** command to check the revision history and
rollback.


But to make this work, we also need to use the **--record** flag when we
use any apply or set commands to modify the Deployment.

Example :

In the previous configuration, we will change the name of the container from "nginx" to "nginx-container"

After we do the modification on the deployment configuration file, we execute the following command:

```

kubectl apply -f deployment.yaml --record

```