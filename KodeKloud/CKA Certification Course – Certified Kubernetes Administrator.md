# CKA Certification Course – Certified Kubernetes Administrator
[GitHub Repo](https://github.com/kodekloudhub/certified-kubernetes-administrator-course)

### Cluster Architecture
- Master: Manage, plan, schedule, monitor Nodes
- Worker Nodes: Hosts application as container

### Master
- ETCD Cluster: DB stores info about nodes in a key value format (DB)
- kube-scheduler: Schedules the right node to place a container. (Crane)
- Controllers: (Control rooms)
  - Node-Controller: On boards nodes, handles their issues
  - Replication-Controller: Ensures desired number of containers is running at all times
- kube-apiserver: Primary management component to orchestrate all operations in a cluster (Master)
### Worker Nodes:
- kubelet: Agent runs on each node, listens to instructions from kube-apiserver, sends reports to it (Captain)
- kubeproxy: Enables communication between services on a cluster (Radio)
ADD IMAGE

#### ETCD
Distributed reliable key-value store on port 2379, specify various etcd instances in the master etcd cluster
- `./etcdctl` more info
  - `--version`
  - `set key value` or `put` in v3
  - `get key`
  - `backup` or `snapshot save` in v3
  - `cluster-health` or `endpoint health`
- `export ETCDCTL_API=3 ./etcdctl CMD` switch command to v3

#### kube-apiserver
Primary management component in kubernetes center of all interactions

#### Controller Manager (Kube-Controller-Manager)
Monitors and controls the state of the system (Brain)
- Node Controller
  - On boards nodes, checks every 5sec, with max of 40sec, 5min to delete node and replicate
- Replication Controller
  - Monitors replicaSets ensuring desired number of PODs is available at all times

#### Kube Schedular
Schedules the right node to place a container on by Kubelet. (Crane)
1. Filters nodes
2. Ranks nodes
### Kubelet
Agent runs on each node, listens to instructions from kube-apiserver, sends reports to it (Captain)
- Is not automatically, deployed by kubeadm, **must by installed manually**
#### Kube-proxy
Process runs on each node, creates rules in tables for newly created service

### Namespace
Set of rules and resources allocated to a group aka **ns**
- Default
- kube-system: for system services, ex: DNS
- kube-public: all resources exposed to users 
``` 
apiVersion: v1
kind: Namespace
metadata: 
  name:
```
- `kubectl get ns` or `kubectl get ns --no-headers | wc -l`
- `kubectl create namespace NAMESPACE` or by file definetion
- OBJ-NAME.NAMESPACE.OBJ.DOMAIN (FQDN: Fully Qualified Domain Name) default domain is cluster.local
- `kubectl get pods --namespace=NAMESPACE` use namespace flag to create pod in another namespace other than default & Can be specified in Pod definition file or `-n NAMESPACE`
6379
- `kubectl config set-context $(kubectl config current-context) --namespace=NAMESPACE` set the context namespace
- `kubectl get pods --all-namespace` OR `kubectl get pods -A`
```
metadata:
  name: 
  namespace:
```

  #### Resource Quota
  Limit resource in a namespace
  ```
  apiVersion: v1
  kind: ResourceQuota
  metadata:
    name:
    namespace:
  spec:
    hard:
      pods:
      requests.cpu:
      requests.memory:
      limits.cpu:
      limits.memory:
  ```

### Imperative vs Declarative approach
- Imperative: Running the step by step of creation or update commands like `edit`, `scale` directly, or using object files with `create` or `update` commands
- Declarative: Declaring the final state and letting the software handle it, by using object files and `apply`
  - `apply` command creates the object if it does't exist, and updates it with the changes, is converted to JSON, stored in the live kubernetes configuration with the key `last-applied-configuration`
    - `--force` completely overrides the old object (Kills then recreates it)

## Scheduling
Schedules the right node to place a container. (Crane)
- If kubeadm is used, it's created as a pod in the `kube-system` namespace
### Manual scheduling
Without a schedular
```
spec:
  nodeName: NODE
```
- To schedule pod after creation use Binding object
```
apiVersion: v1
kind: Binding
metadata:
  name:
target:
  apiVersion:
  kind: Node
  name: NODE-NAME
```
Which is sent to the binding api with the binding object data in a JSON format

#### Taints and Tolerations
Tells node to accept pods with certain toleration.
- Taint: Applied to node to repel pods with no tolerations
- Toleration: The ability of pods to be scheduled on tainted nodes
- `kubectl taint nodes NODE KEY=VALUE:TAINT-EFFECT`
  - Taint Effect:
    - NoSchedule | PreferNoSchedule | NoExecute: Ejects running non-tolerated pods
      - Add a `-` at the end to remove taint
- In pod definition
```
spec
  tolerations:
  - key: ""
    operator: ""
    value: ""
    effect: ""
```

#### Node Selectors
- `kubectl label nodes NODE KEY=VALUE` @Node
- @Pod
```
spec:
  nodeSelector:
    key: value
```
- Limitations: Cannot use OR or NOT

#### Node Affinity
Prevent pods to be placed on random nodes
- Types
  - Available
    -  requiredDuringSchedulingIgnoredDuringExecution 
    - preferredDuringSchedulingIgnoredDuringExecution
  - Planned
    -  requiredDuringSchedulingRequiredDuringExecution
    - preferredDuringSchedulingRequiredDuringExecution
```
spec:
  affinity:
    nodeAffininty:
      requiredDuringSchedulingIgnoredDuringExecution: (ex)
        nodeSelectorTerms:
        - matchExpressions:
          - key: size (an example for searching by size)
            operator: {In, NotIn, Exists(no need for values)}
            values:
            - VAL
            - VAL
  nodeSelector:
    key: value
```

#### Resource Limits
```
spec:
  containers:
    resource:
      requests:
        memory: "4Gi" (default bytes, Gi Gibibytes, G Gigabytes,.....)
        cpu: 2 (1m (milli), ....)
      limits:
        memory:
        cpu:
```
- If no requests is set, it's set to the limit
- If cpu usage exceeds limits, cpu throttles
- If memory usage exceeds limits, it stops pod with OOM (Out Of Memory)

- **Limit Range**
Sets default values for containers inside pods
```
apiVersion: v1
kind: LimitRange
metadata:
spec:
  limits:
  - default: (limit)
      cpu: 500m
    defaultRequest:(request)
      cpu: 
    max:(limit)
      cpu:
    min: (request)
      cpu:
    type: Container
```
and same for memory

- Running pod limits cannot by updated in runtime
  1. Edit running configuration, replace running pod with the temporary saved copy of editing
    - `kubectl edit pod POD` + `kubectl replace -f --force FILE`
  2. `kubectl get pod POD -o yaml > FILE` + edit it + delete running pod + recreate the pod
  

#### Resource Quotas
Namespace level object, sets hard limits for requests and limits for all pods together
```
apiVersion: v1
kind: ResourceQuota
metadata:
  name:
  namespace:
spec:
  hard:
    pods:
    requests.cpu:
    requests.memory:
    limits.cpu:
    limits.memory:
```

#### Daemon Sets
Ensures that one copy of the pod is always present in all nodes in the cluster. (ex: logs, kube-proxy)
- Located in `kube-system` namespace
- `kubectl get daemonsets`
- Uses NodeAffinity rules to locate pods to specific nodes
- Same as ReplicaSet but locates only one pod per node
```
apiVersion: apps/v1
kind: DaemonSet 
spec:
  template: 
  replicas: 
  selector: (Mandatory)
    matchLabels:
      key: value 
```

#### Static Pods
Without a Master, the kubelet can deploy, update, delete automatically pods from a directory files, used to deploy controlplane components.
- Can be viewed as a copy by kube-apiserver, but cannot delete or modify them.
- It's pod name ends with `-controlplane` as it is it's node name
- Set static directory
  1. In `kubelet.service` file, directory is set with property `--pod-manifest-path`
  2. In `kubelet.service` file, provide `--config` file, in config file provide `staticPodPath:` with the directory path.
    - Usually the config file is in `/var/lib/kubelet/config.yaml`
- `docker ps` to view static pods, if don't have kubernetes cluster yet
