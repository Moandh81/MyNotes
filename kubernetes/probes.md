# Probes and Heakth checks


A probe is a health check that can be configured
to check the health of containers running in a pod.

A probe can be used to determine wether a container is running or ready to receive requests

A probe my return the following results :

- Success : The container passed the health check.
- Failure : The container failed the health check.
- Unknown : The health check failed for unknown reasons.

## Liveness Probe

This is a health check that's used to determine whether a particular container is
running or not.


 If a container fails the liveness probe, the controller will try to restart the pod on the same node according to the restart policy configured for the pod.

 ## Readiness Probe


 This is a health check that's used to determine whether a particular container is ready
to receive requests or not.

How we define the readiness of a container depends
largely on the application running inside the container.



If a container fails its readiness probe, the Kubernetes controller will ensure that
the pod doesn't receive any requests.


If a container specifies a readiness probe, its
default state will be **Failure** until the readiness probe succeeds.


The container will
start receiving requests only after the readiness probe returns with the **Success**
state. If no readiness probe is configured, the container will start receiving requests
as soon as it starts.


## Implementation of probes

Probes (liveness or readiness) can be implemented by passing a command to the
container, getting it to fetch some resources, or trying to connect to it, as we shall
see in this section.

### The command probe 

n the command implementation of a probe, the controller will get the container to
execute the specified command in order to perform the probe on the container. For
this implementation, we use the **command** field.


This field specifies the command to
execute in order to perform the probe on the container. It can either be a string or
an array.

Example :


```

livenessProbe:
  exec:
    command:
    - cat
    - /tmp/health
  initialDelaySeconds:
  periodSeconds: 15
  failureThreshold: 3
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/health
  initialDelaySeconds:
  periodSeconds: 15


```


### The HTTP probe

In this type of probe, the controller will send a GET HTTP request to the given address
(host and port) to perform the probe on the container. 

It's possible to set the custom
HTTP headers to be sent in the probe request.


We can set the following fields to configure an HTTP request probe:

- host : Hostname to which the request will be made. It defaults to the pod IP address.
- path : Path to make the request to.
- port : Name or number of the port to make the request to.
- httpHeaders : Custom headers to be set in the request.
- scheme : Scheme to use while making the request. The default value is HTTP.


Here's an example of an HTTP request probe for liveness and readiness:



```

livenessProbe:
  httpGet:
    path: /health-check
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 20
readinessProbe:
  httpGet:
    path: /health-check
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10

```


### TCP socket port


In this implementation of a probe, the controller will try to establish a connection on
the given host and the specified port number. We can use the following two fields for
this probe:

- host : Hostname to which the connection will be established. It defaults to the
pod IP address.

- port : Name or number of the port to connect to.


Example :


```

livenessProbe:
  tcpSocket:
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 20
readinessProbe:
  tcpSocket:
    port:8080
  initialDelaySeconds: 5
  periodSeconds: 10



```

## Restart Policy


We can specify **restartPolicy** in the pod specification to instruct the
controller about the conditions required to restart the pod.

The default value
of **restartPolicy** is **Always** .

 It can take the following values:

- **Always** : Always restart the pod when it terminates.

- **OnFailure** : Restart the pod only when it terminates with failure.

- **Never** : Never restart the pod after it terminates.


If we want the pod to crash and restart when it has some issues or becomes
unhealthy, we should set the restart policy to either **Always** or **OnFailure** .


Example :


```

apiVersion: v1
kind: Pod
metadata:
  name: liveness-probe-never-restart
spec:
  restartPolicy: Never
  containers:
    - name: ubuntu-container
      image: ubuntu
      command:
        - /bin/bash
        - -ec
        - touch /tmp/ready; sleep 30; rm /tmp/ready; sleep 600
      livenessProbe:
        exec:
          command:
            - cat
            - /tmp/ready
        initialDelaySeconds: 5
        periodSeconds: 5


```