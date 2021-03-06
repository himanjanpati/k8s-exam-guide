k8s objects clould be boradly classified into two categories based on the accessiblity/scope

    1- Namespaced
    2- Global/Cluster

The below command will list all api resouces avaible
<b>Namesspaced true/false</b> will show object's scope

example - 
```
        NAMESPACED 
pod       true
nodes     false
```
```
kubectl api-resources

```

Deleting the namespace will delete all the resources inside.

Attaching a label to k8s objects makes easy to identify/search/filter the objects based on the label.

Ex - Below is yaml specification for labels 
metadata:
 labels:
    env: dev
    name: nginx-web-app
 name: nginx-app

kubectl imprative way of assigning labels

```
kubectl run nginx-app --image nginx --labels='name=nginx-web-app,env=dev' --dry-run=client -o yaml

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    env: dev
    name: nginx-web-app
  name: nginx-app
spec:
  containers:
  - image: nginx
    name: nginx-app
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

kubectl run nginx-app --image nginx --labels='name=nginx-web-app,env=dev'

$ kubectl get po -l "name=nginx-web-app"
NAME        READY   STATUS    RESTARTS   AGE
nginx-app   1/1     Running   0          2m53s
```
<b>Environment variables</b></br>
environment variables will be placed under container
each container wil have own env variables in multicontainer pods

```
kubectl run nginx-pod --image nginx --env "type=frontend" --dry-run=client -o yaml

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-pod
  name: nginx-pod
spec:
  containers:
  - env:
    - name: type
      value: frontend
    image: nginx
    name: nginx-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
To check the value of the env variables set in the pod printenv | grep <env_variable_name></br>
<b> env variables will not be set using kubectl if the variable name contains a '-' </b>
```
kubectl exec -it nginx-pod -- /bin/sh

printenv | grep type
pod=frontend
```

<b>Probes</b>

Liveness and Readiness probes
probes to health check 

adding liveness probe will check the health status of the pod after the initialdelayseconds
It will keep on restarting till it gets 200 status code response if the liveness probe fails

default value for initialDelaySeconds: 0, min value: 0

periodSeconds: How often (in seconds) to perform the probe. 
Default to 10 seconds. Minimum value is 1.

```
livenessProbe:
  httpGet:
    path: /
    port: 80
initialDelaySeconds: 30
periodSeconds: 20
```
adding readiness probe will check the health status of the pod in each interval of initialdelayseconds and will not make the status of the pod to ready until the check is passed
```
readinessProbe:
  httpGet:
    path: /
    port: 80
initialDelaySeconds: 30
periodSeconds: 15
```

Note - Liveness probe restarts the pod if the check fails where as readiness probe will not reastart the pod

HTTP probes:
For an HTTP probe, the kubelet sends an HTTP request to the specified path and port to perform the check.

