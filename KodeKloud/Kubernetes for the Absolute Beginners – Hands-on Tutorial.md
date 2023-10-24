# Kubernetes for the Absolute Beginners
Open-source container orchestration system for automating software deployment, scaling, and management.
  - Uses `kubectl` (Kubernetes cli tool)
    - `create -f YAML-FILE` create any Kubernetes object from yml file
    - `apply -f YAML-FILE` apply changes in yaml file to Pod
    - `run POD-NAME` deploys a container by creating a POD
      - `--image=IMG-SRC`
      - `--dry-run=client -o yaml > FILE-NAME.yaml` *Automatically* create .yaml file for the specified pod
    - `edit pod POD-NAME` edits the long Pod description
    - `describe pod POD-NAME` provides detailed info on a POD
    - `get`
      - `pods` or `nodes` or `replicationcontroller` or `replicaset`
        - `-o wide` more info
    - `delete`
      - `pods` or `nodes` or `replicationcontroller` or `replicaset`
    - `replace -f YAML-FILE` replace a kubernetes object
    - `scale --replicas=N NAME-OR-FILE` scale a replicaset
    - `cluster-info`

##  Docker vs ContainerD
- Docker is unsupported by kubernetes anymore
- ContainerD uses:
  - `ctr`: a cli to debug containers
  - `nerdctl`: a cli to replace docker utilities 
- Kubernetes:
  - CRI: Container Runtime Interface for any container runtime
  - OCI: Open Container Initiative for unified images (imagespec) and container runtime (runtimespec)
  - `crictl`: a separate cli to inspect and debug CRI compatible runtime

## YAML in Kubernetes
Creating object with YAML files
```
apiVersion: {v1, v1, apps/v1, apps/v1}
kind: {Pod, Service, ReplicaSet, Deployment}
metadata:
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
- ReplicaSet
```
apiVersion: apps/v1
kind: ReplicaSet
SAME AS REPLICACONTROLLER
spec:
  template: POD-TEMP
  replicas: 
  selector: (Mandatory)
    matchLabels:
      key: value 
```
- `kubectl delete replicaset YAML-FILE` also deletes it's PODs
- `kubectl scale --replicase=N -f YAML-FILE` scales up the app without modifying the original yaml file