# CKA Certification Course – Certified Kubernetes Administrator

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
- `kubectl get pods --all-namespace`
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
  - `apply` command creates the object if it does't exist, and updates it with the changes
    - `--force` completely overrides the old object