# Certified Kubernetes Application Developer (CKAD)
- `kubectl scale --replicas=N DEP/SVC DEP/SVC-NAME` Scale rs (Not updated in OG definition file)
- `kubectl config set-context $(kubectl config current-context) --namespace=NS` To specify the default Namespace
  - `--namespace == -n`
- `expose pod redis --port=6379 --name redis-service` create clusterIP service (Default type)
- `create service clusterip redis --tcp=6379:6379` same
- `expose pod nginx --port=80 --name nginx-service --type=NodePort` create NodePort service
- `create service nodeport nginx --tcp=80:80 --node-port=30080` same
- `kubectl run httpd --image=httpd:alpine --port=80 --expose` create a pod and it's service with the same name <<
- `docker build -t IMG-NAME FILE` Create the image **Must be logged in first `docker login`**
- `docker push ACCOUNT/IMAGE-NAME` Push image to docker hub **Must have a tag name**
- `kubectl ......... -- ARGS` For in-container arguments
- `kubectl taint nodes NODE-NAME KEY=VALUE:TAINT-EFFECT` effect: NoSchedule | PreferNoSchedule | NoExecute (Refer to CKA)
- `kubectl logs -f MULTI-CONTAINER-POD -c CONTAINER` stream live logs **Must specify container in case of a multi-container pod**
- `kubectl get pods --selector KEY=VALUE` Filter objects by labels
  - `KEY=VALUE,KEY=VALUE` combine selectors
- `kubectl get job`
- `kubectl get cronjob`

### Namespaces
#### ResourceQuota
Specify resource limits for namespace
```
apiVersion: v1
kind: ResourceQuota
metadata:
  namespace:
spec:
  hard:
    ....
```
## Multi-Container Pods
In microservice architecture, we may want two pods to be developed and deployed separately, but work together, sharing same life cycle, network, storage
### Design Patterns
All pattern are implemented the same as as array of containers in the pod definition file.
1. Sidecar: ex Logging agent working with a server
2. Adapter: ex Processing the logs before sending them to the central server
3. Ambassador: ex Modify connectivity in application code depending on the environment

#### Init Containers
Containers in pod which *must run to completion sequentially* before the real containers starts.
```
spec:
  containers: ....
  initContainers: ...
```

## Observability
### Probes
#### Readiness Probes
Define what it means for an application inside that a container to be ready before it's being used (Depending on app).
```
spec: (In Pod definition file)
  containers:
    readinessProbe: (One of the following)
      httpGet:
        path:
        port: (OR)
      tcpSocket: 
        port: (OR)
      exec: 
        command:
          - 
  
  initialDelaySeconds: (Fixed delay to warmup)
  periodSeconds: (How often to probe)
  failureThreshold: (Default for probing is 3 attempts)
```
#### Liveness Probes
Periodically test whether the application inside the container is actually healthy.
```
spec.containers.livenessProbes: (Same properties as Readiness Probes)
```
### Logging
In case of a multi-container pod, we must specify the container in pod to view it's logs
- `kubectl logs -f MULTI-CONTAINER-POD -c CONTAINER` stream live logs **Must specify container in case of a multi-container pod**
### Monitoring
Kubernetes doesn't come with a monitoring solution
- Open source solutions: METRICS SERVER, DATADOG .....
- METRICS SERVER:
  - Open source In-memory storage solution, no historical data stored on disk
  - `kubelet` uses `cAdvisor` to send performance metrics through kubelet api to METRICS SERVER
  - `minikube addons enable metrics-server` if using minikube OR Download from [GitHub](https://github.com/kodekloudhub/kubernetes-metrics-server) and create it's components via `kubectl -f .` in the downloaded repo.
  - `kubectl top` to get CPU & memory usage
    - `pod` or `node`

## POD Design
### Labels, Selectors and Annotations
Labels for grouping, Selectors for filtering
- `kubectl get pods --selector KEY=VALUE` Filter objects by labels
  - `KEY=VALUE,KEY=VALUE` combine selectors
#### Annotation
Records other details for informatory purposes, ex: version, contact info or integration purposes.
```
metadata:
  annotations:
    KEY: VALUE
```
#### Rolling updates and Rollbacks
Deployment >> Rollout >> Revision
1. Recreate: Destroy all of instances and redeploy them
2. Rolling Update: (Default) take down and recreate instances one by one
- `kubectl rollout status DEPLOY`
- `kubectl rollout history DEPLOY`
- `kubectl rollout undo DEPLOY`
  - `--to-revision=e` rollback to a specific version
  - `--revision=N` to specify revision number, -1 is the first default on spawn-
  - `--record` save command used to create/update deployment

- `kubectl apply -f FILE` to updated deployment 
  - `set image DEPLOY CONTAINER-NAME=NEW-IMAGE` to update deployment image **It results in deployment definition file having a different configuration**

#### Deployment Strategy
- Recreate
- RollingUpdate -Default-
- Blue/Green: v2 is deployed alongside v1, after all tests pass, route the traffic there.
- Canary: Route small amount of traffic to the v2 -using relatively smaller number of pods, or service manager like `Istio`-, if everything looks good upgrade original deployment with v2

#### Jobs
Run a set of pods to perform a given task to completion.
```
apiVersion: batch/v1
kind: Job
metadata:
spec:
  completions: N -Like replicas is rs-
  parallelism: N -Default is sequential execution-
  template:
    spec:
      containers:
      - .....
      restartPolicy: 
```
- In POD def file
  - `resartPolicy: {Always -default-, Never, Onfailue}` Defines what happens when a container exits
  - `backoffLimit: N` specify the number of retries before considering a Job as failed.
#### CronJobs
A job the can be scheduled
```
apiVersion: batch/v1beta
kind: CronJob
metadata:
spec:
  schedule: "* * * * *" -Cron format string-
  jobTemplate:
    -JOB-
```
```
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of the month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday;
# │ │ │ │ │                                   7 is also Sunday on some systems)
# │ │ │ │ │                                   OR sun, mon, tue, wed, thu, fri, sat
# │ │ │ │ │
# * * * * *
```
