# Kubernetes for the Absolute Beginners

Open-source container orchestration system for automating software deployment, scaling, and management.
  - Uses `kubectl` (Kubernetes cli tool)
    - **`--help` for help with rest of command**
    - `explain OBJ` explains a kubernetes object
    - `create`
      - `-f YAML-FILE` create any Kubernetes object from yml file
      - `OBJ` 
    - `apply -f YAML-FILE` apply changes in yaml file to Pod
    - `run POD-NAME` deploys a container by creating a POD
      - `--image=IMG-SRC`
      - `--port=PORT`
      - `--labels="KEY=VALUE"` OR `-l key=value` specify label
      - `--dry-run=client -o yaml > FILE-NAME.yaml` *Automatically* create .yaml file for the specified pod, `--dry-run=client` ensures that command is right and resource can be created, `-o yaml` outputs resource definition in yaml extension
      - `--command -- COMMANDS` Don't specify any kubectl arguments after --command (use it at the end)
      - `--expose` automatically create a ClusterIP service and exposes the pod, we can specify name and port by `--port PORT --name NAME`
    - `get`
      - `pod` or `node` or `replicationcontroller` or `replicaset` or `deployment` or `service` or **`event`** or **`all`**
        - `--selector KEY=VALUE`
        - `--watch` watch changes in realtime
    - `edit` opens the long running configuration (In memory file by kubernetes)
      - `pod` or `node` or `replicationcontroller` or `replicaset`
        - `-o wide` more info
    - `describe` by object name
      - `pod` or `node` or `replicationcontroller` or `replicaset`
    - `delete` by object name or file
      - `pod` or `node` or `replicationcontroller` or `replicaset`
    - `replace -f YAML-FILE` replace a kubernetes object
      - `--force` kills then recreates object
    - `expose pod POD-NAME --port=PORT --name=SERVICE-NAME` create a ClusterIP service exposes an application on a port
    - `scale --replicas=N NAME-OR-FILE` scale a replicaset
    - `cluster-info`
    - `rollout` see status
      - `status`
      - `history`
      - `undo` rollback a deployment
  - `minikube service SERVICE --url` gets url for service
  - `logs OBJ-NAME`
##  Docker vs ContainerD
- Docker is unsupported by kubernetes anymore
- ContainerD uses:
  - `ctr`: a cli to debug containers
  - `nerdctl`: a cli to replace docker utilities 
- Kubernetes:
  - CRI: Container Runtime Interface for any container runtime
  - OCI: Open Container Initiative ior unified images (imagespec) and container runtime (runtimespec)
- kubernetes ues:
  - `crictl`: a separate cli to inspect and debug CRI compatible runtime

## YAML in Kubernetes
Creating object with YAML files
```
apiVersion: {v1, v1, apps/v1, apps/v1}
kind: {Pod, Service, ReplicaSet, Deployment}
metadata: (includes only special properties)
  name:
  labels:
    key: value
spec:
  containers: 
    - name:
      image:
```

## Replication Controller and ReplicaSets
- Load balances and scales the application across PODs and nodes.
- ReplicaController
```
apiVersion: v1
kind: ReplicationController
metadata:
  name:
  labels:
    key: value
spec:
  template:
    metadata:
      name:
      labels:
        key: value
    spec:
      containers: 
        - name:
          image:
  replicas: n
```
- ReplicaSet aka `rs`
```
apiVersion: apps/v1
kind: ReplicaSet
SAME AS REPLICACONTROLLER
spec:
  template: POD-TEMP (metadata + spec)
  replicas: 
  selector: (Mandatory)
    matchLabels:
      key: value 
```
- `kubectl delete replicaset REPLICA-NAME` also deletes it's PODs
- Scale replicaset
  - `kubectl scale --replicaset=N rs REPLICA-NAME` scales the app without modifying the original yaml file
  - `kubectl edit replicaset REPLICA-NAME` Opens kubernetes in-memory file to edit running configuration

## Deployment
Enable declarative updates to Pods and ReplicaSets aka `deploy`
- Same YAML as a ReplicaSet where `kind: Deployment`
- On creation it triggers a *Rollout* which create a *Revision*   
- `kubectl create -f DEPLOYMENT --record` records the change cause to be displayed in the history entry
- `kubectl rollout status DEPLOYMENT` see status
- `kubectl rollout history DEPLOYMENT` see history and revisions of the deployment
- `kubectl set image deployment DEPLOY CONT-NAME=NEW-IMG` set a container image in deployment

### Deployment strategy
- Recreate: Destroy all instances the recreate them
- RollingUpdate: Destroy instance and create another one, one by one (Default)

### Upgrades
Creates a new replicaset, starts deploying to it
- `kubectl rollout undo DEPLOYMENT` rollback a deployment

## Networking
- IP is assigned to a POD in the internal kubernetes private network 10.224.x.x
- A Networking solution is needed to manage networks and IPs, assign different network addresses for each network in the node, and enables communication between pods and nodes.

## Services
- Enables communication between components within and outside our application aka `svc`
### 1. NodePort
Creates internal POD accessible on a port on the node
  - Runs across all nodes in cluster
  - ![NodePort Diagram](https://www.bogotobogo.com/DevOps/Docker/images/Docker-Kubeernetes-Pods-Services/Service-Port-NodePort-TargetPort.png)
  ```
  apiVersion: v1
  kind: Service
  metadata:
    name:
  spec:
    type: NodePort
    ports:
      - targetPort: 80
        port: 80 (The only mandatory field, Assumes targetPort to be the same, automatically sets nodePort to a free port)
        nodePort: 30008
    selector:
      key: value (to specify pods linked to the service)
  ```
  - `minikube service SERVICE --url` gets url for service

### 2. ClusterIP (Default)
- Create a virtual IP inside cluster to enable communication between services
- ![ClusterIP Diagram](https://miro.medium.com/v2/resize:fit:1200/1*dLlC4L2qpImyZS6gOntUjg.png)
```
  spec:
    type: ClusterIP
    ports:
      - targetPort: x
        port: x
    selector:
      key: value
```
### 3. loadbalancer
- provisions a load balancer in supported cloud providers
- ![clusterip diagram](https://www.ionos.co.uk/digitalguide/fileadmin/digitalguide/schaubilder/overview-of-how-kubernetes-load-balancers-work.png)
```
spec:
  type: LoadBalancer
```

## kubeadm
Helps setup a multi node cluster networking & security + **Vagrant** to build and maintain VMs
- `kubeadm` bootstrap the cluster
- `kubelet` component that runs all machines on cluster, start pods and containers
- `kubectl` command line util to talk to cluster