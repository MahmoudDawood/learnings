# CKA Certification Course – Certified Kubernetes Administrator
[GitHub Repo](https://github.com/kodekloudhub/certified-kubernetes-administrator-course)
- `./etcdctl` more info
  - `--version`
  - `set key value` or `put` in v3
  - `get key`
  - `backup` or `snapshot save` in v3
  - `cluster-health` or `endpoint health`
- `export ETCDCTL_API=3 ./etcdctl CMD` switch command to v3
- `kubectl get ns` or `--no-headers | wc -l`
- `kubectl create namespace NAMESPACE` or by file definetion
- OBJ-NAME.NAMESPACE.OBJ.DOMAIN (FQDN: Fully Qualified Domain Name) default domain is cluster.local
- `kubectl get pods --namespace=NAMESPACE` use namespace flag to create pod in another namespace other than default & Can be specified in Pod definition file or `-n NAMESPACE`
6379
- `kubectl config set-context $(kubectl config current-context) --namespace=NAMESPACE` set the context namespace
- `kubectl get pods --all-namespace` OR `kubectl get pods -A`
- `kubectl taint nodes NODE KEY=VALUE:TAINT-EFFECT`
- `kubectl label nodes NODE KEY=VALUE` @Node
- `kubectl get daemonsets`
- `kubectl logs OBJ-OR-FILE` view object logs
  - `-c CONTAINER` to specify container logs inside pod
  - `minikube addons enable metrics-server` if using minikube OR Download from [GitHub](https://github.com/kodekloudhub/kubernetes-metrics-server) and create it's components
  - `kubectl top` to get CPU & memory usage
    - `pod` or `node`
- `kubectl get configmaps` or `cm`
- `kubectl create configmap NAME` Imperative approach, same for `secret`
  - `--from-literal=KEY=VALUE` OR `--from-file=FILE`
- `kubectl create -f CONFIG-FILE` Declarative approach
- `echo -n 'secret' | base64` to encode secret before storing it in file
- `kubectl get secret NAME -o yaml` to view the secret values
- `echo -n 'secret' | base64 --decode` to decode a secret 
- `head -c 32 /dev/urandom | base64` generate a random key
- `kubectl get secrets --all-namespaces -o json | kubectl replace -f -` Ensure all old data are encrypted
- `kubectl ......... -- ARGS` For non-kubectl arguments
- `kubectl exec -it POD -- COMMAND` execute a command in a pod

  - `config`
    - `view` view all configured clusters, **kubectl** must be installed
    - `use-context CLUSTER`

- `etcdctl member list` to see all nodes that are a part of this ETCD server, *pass the flags to connect to the server whenever you use `etcdctl`*

- `config view` to view currently running config file
  - `--kubeconfig=CONFIG-FILE`
- `config use-context CONTEXT` Change context
- `proxy` launches a local HTTP proxy service on port 8001 to access kube-apiserver, uses credentials and certificates from kubeconfig to access cluster
- `get roles` + all other operations
- `get rolebindings` + all other operations + CLUSTER OBJ
- `auth can-i VERB RESOURCE` check current access
- `--as ANOTHER-USER` Administrator makes action as a user
- `api-resources` Get resources
  - `--namespaced={true,false}` {namespaced, cluster scoped}
- `get serviceaccount` or `sa`
- `get networkpolicy` or `netpol`
- `get persistentvolume` or `pv`
- `get persistentvolumeclaim` or `pvc`
- `get storageclass` or `sc`
- `get ingress`

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

#### Kube Scheduler
Schedules the right node to place a container on by Kubelet. (Crane)
1. Filters nodes
2. Ranks nodes
### Kubelet
Agent runs on each node, listens to instructions from kube-apiserver, sends reports to it (Captain)
- Is not automatically deployed by kubeadm, **must by installed manually**
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
- Requests: Initial minimum located resource to service (Recommended to be set)
- Limits: Maximum allowed resources limits to be located

### Imperative vs Declarative approach
- Imperative: Running the step by step of creation or update commands like `edit`, `scale` directly, or using object files with `create` or `update` commands
- Declarative: Declaring the final state and letting the software handle it, by using object files and `apply`
  - `apply` command creates the object if it does't exist, and updates it with the changes, is converted to JSON, stored in the live kubernetes configuration with the key `last-applied-configuration`
    - `--force` completely overrides the old object (Kills then recreates it)

## Scheduling
Schedules the right node to place a container. (Crane)
- If kubeadm is used, it's created as a pod in the `kube-system` namespace
### Manual scheduling
Without a scheduler
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
  - key: "" (Double quotes are essential in all fields)
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
Prevent pods to be placed on random nodes *allowing operators on node selectors*
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
    nodeAffinity:
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
Located to pods
```
spec:
  containers:
    resource:
      requests:
        memory: "4Gi" (default bytes, Gi Gibibytes, G Gigabytes,.....)
        cpu: 2 (1m (milli cores) - ....)
      limits:
        memory:
        cpu:
```
- **If no requests is set, it's set to the limit**
- If cpu usage exceeds limits, cpu throttles
- If memory usage exceeds limits, it stops pod with OOM (Out Of Memory)

- **Limit Range**
Sets default predefined values for containers inside pods
```
apiVersion: v1
kind: LimitRange
metadata:
spec:
  limits:
  - default: (limit)
      cpu: 500m
    defaultRequest: (request)
      cpu: 
    max: (limit)
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
- Default definition files are in `/etc/kuberenets/manifests`
- Set static directory
  1. In `kubelet.service` file, directory is set with property `--pod-manifest-path`
  2. In `kubelet.service` file, provide `--config` file, in config file provide `staticPodPath:` with the directory path.
    - Usually the config file is in `/var/lib/kubelet/config.yaml`
- `docker ps` to view static pods, if don't have kubernetes cluster yet
- Or have a look at `kube-apiserver` configuration, if it points to internal IP address or local host it's stacked, else if it's an external IP address, then it's using an external ETCD server
- `ps -ef | grep OBJ` to view running objects

#### Multiple Schedulers
Configure multiple custom schedulers
- Deploy additional scheduler as a pod:
```
(POD)
spec:
  containers:
  - command:
    - kube-scheduler
    - --address=
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --config=/etc/kubernetes/SCHEDULER-CONFIG-FILE
```
- Scheduler configuration file
```
apiVersion: kubescheduler.conf.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
  - schedulerName: 
leaderElection: (In case it runs on multiple master nodes for HA, only one can be active at a time)
  leaderElect:
```
- Pod to run scheduler
```
spec:
  schedulerName:
```
- `kubectl logs SCHEDULER-NAME` view scheduler logs

#### Scheduler Profiles
Configures multiple profiles within a single schedular configuration file, can be configured to work differently.
```
apiVersion: kubescheduler.conf.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
  - schedulerName: 
    plugins:
      EXTENSION:
        disabled:
          - name:
        enabled:
          - name:
  - schedulerName:
    ....
```

- Scheduling Plugins: to schedule a pod 
  - Scheduling Queue: (PrioritySort) we can configure a Priority type object, specify it's value, use it's name to give pods a priority
  - Filtering: (NodeResourceFit, NodeName -filters specified nodeName property in pod definition-, NodeUnschedulable -filters Unschedulable nodes flag set to true)
  - Scoring: (NodeResourceFit, ImageLocality -higher score for nodes where images are located)
  - Binding: (DefaultBinder)
- Extension Points: extends plugins for each phase some examples:
  - Scheduling Queue: (queueSort)
  - Filtering: (filter)
  - Scoring: (score)
  - Binding: (bind)

### Logging and Monitoring
#### Monitor Cluster Components
Many additional tools help monitor kubernetes cluster components
- METRICS SERVER:
  - In-memory storage, no historical data stored on disk
  - `kubelet` uses `cAdvisor` to send performance metrics through kubelet apit to METRICS SERVER
  - `minikube addons enable metrics-server` if using minikube OR Download from [GitHub](https://github.com/kodekloudhub/kubernetes-metrics-server) and create it's components
  - `kubectl top` to get CPU & memory usage
    - `pod` or `node`

#### Application logs
- `kubernetes logs POD-CONF-FILE`
  - `-f` option to stream logs live
  - Must specify container name if there's multiple containers on the same pod

## Application Lifecycle Management
#### Rolling updates and Rollbacks
Deployment >> Rollout >> Revision
1. Recreate: Destroy all of instances and redeploy them
2. Rolling Update: (Default) take down and recreate instances one by one
- `kubectl rollout status DEPLOYMENT`
- `kubectl rollout history DEPLOYMENT`
- `kubectl rollout undo DEPLOYMENT`

#### Commands and Arguments
* For a custom full command to run on the image deployment, create your own image with:
  - `CMD ["sleep", "10"]` **always separate arguments**
* For a command to run at startup
  - `ENTRYPOINT ["sleep"]` specify the time argument after the docker run command, can be overridden while running the container by specifying `--entrypoint ENTRYPOINT`
* For a combination of default command + default arguments
  - `ENTRYPOINT ["sleep"] \ CMD ["5"]` where the 5 acts as a default parameter if none is provided
- The above image configuration can be overridden in the pod
  - `args` >> `CMD`, `command` >> `ENTRYPOINT`  under the `spec.containers` container specification

#### Environment Variables
```
spec:
  - name:...
    env: (OR) envFrom: (List of configuration maps)
    - name:
      value: (OR) valueFrom:
                    configMapKeyRef: (OR) secretKeyRef: 
                      name: CONFIG-FILE-NAME
                      key: KEY
```
#### ConfigMaps
Manage non-confidential configuration data as a key-value pairs centrally. aka `cm`
1. Create ConfigMap
2. Use it in pods
- `kubectl get configmaps`
- `kubectl create configmap NAME` Imperative approach
  - `--from-literal=KEY=VALUE` OR `--from-file=FILE`
- `kubectl create -f CONFIG-FILE` Declarative approach
  - ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
    data:
      key:value
  
#### Secrets
Store sensitive information in an *encoded* format (Same as ConfigMap)
1. Create the secret
  - `kubectl create secret generic NAME` Imperative approach, generic is the default secret type
    - `--from-literal=KEY=VALUE` OR `--from-file=FILE`
  - `kubectl create -f SECRET-FILE` Declarative approach
    - ```
      apiVersion: v1
      kind: Secret 
      metadata:
      data:
        key: encoded-value
2. Inject it into pod
  - ```
    spec:
      - .....
        envFrom: (List of configuration maps)
          - secretKeyRef: or (secretRef) for selecting secret file directly 
              name: SECRET-NAME
- `echo -n 'secret' | base64` to encode secret before storing it in file
- `kubectl get secret NAME -o yaml` to view the secret values
- `echo -n 'secret' | base64 --decode` to decode a secret 
- Notes
  - Secrets are encoded NOT encrypted
  - Secrets are not encrypted in ETCD, we have to enable [encryption at rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)
  - Secrets are available to anyone who can create pods/deployments in namespace
    - Consider Role Based Access Control (RBAC)
  - Consider 3rd party secret providers 

### Enable Encryption at rest
#### View secrets from etcd with hexdump
```
ETCDCTL_API=3 etcdctl \
   --cacert=/etc/kubernetes/pki/etcd/ca.crt   \
   --cert=/etc/kubernetes/pki/etcd/server.crt \
   --key=/etc/kubernetes/pki/etcd/server.key  \
   get /registry/secrets/default/SECRET-TO-SEE | hexdump -C
```
### EncryptionConfiguration
- Create `EncryptionConfiguration` object file, pass it as an option
```
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
      - configmaps
    providers:
      - identity: {} (What's being used for encryption)
      - aescbc:
          keys:
            - name: KEY1
              secret: <BASE 64 ENCODED SECRET>
```
- `head -c 32 /dev/urandom | base64` generate a random key
- to use the encryption configuration >> `/etc/kubernetes/manifests/kube-apiserver.yaml`
  - add the encryption provider as command `--encryption-provider-config=/etc/kubernetes/enc/enc.yaml` Destination of the encryption configuration
  - Add mounts and volume mounts
  -  ```
      volumeMounts:
      - name: enc                           # add this line
        mountPath: /etc/kubernetes/enc      # add this line
        readOnly: true                      # add this line
    volumes:
    ...
    - name: enc                             # add this line
      hostPath:                             # add this line
        path: /etc/kubernetes/enc           # add this line
        type: DirectoryOrCreate 
- `kubectl get secrets --all-namespaces -o json | kubectl replace -f -` Ensure all old secrets are encrypted

#### Multi Container Pod
In microservice, we may want services functionality to work together, but developed and deployed separately.
- Design Patterns: Discussed in CKAD course
  - Sidecar
  - Adapter
  - Ambassador


#### initContainers
Task runs once when pod is first created, or a process that waits for another service to be up before starting application.
- Must run to completion before the application starts, or it restarts repeatedly.
```
spec:
  containers:
  initContainers:
```

## Cluster Maintenance
### Node Upgrade
- `kube-controller-manager --pod-eviction-timeout=50m0s` pod evection timeout default to 5min
- `kubectl drain NODE` marks node as unschedulable, pods are evicted to running nodes
- `kubectl cordon NODE` mark node as unschedulable, does not terminate running pods
- `kubectl uncordon NODE` remove cordon
### Cluster Upgrade
#### Kubernetes Versions
- vMAJOR.MINOR.PATCH
  - Core controlplance components have same version of kuberentes except ETCD cluster, CoreDNS as they're external components.
  - kube-apiserver @X
  - Control-manager, kube-schedular @X-1 | X
  - kubelet, kube-proxy @X | X-1 | X-2
  - kubectl @X | X-1 | X+1
- Kubernetes support the last 3 minor version only
- I'ts advised to upgrade one version at a time
- Using kubeadm
  - **kubeadm doesn't install or upgrade kubelets**
  - `kubeadm upgrade plan` see all details about upgrading
  - `kubeadm upgrade apply VERSION` upgrade master components
  1. Upgrade kubeadm, run commands from [guide](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)
    - `kubectl drain NODE --ignore-daemonsets`
    - `apt update`
    - `apt-get install kubeadm=1.27.0-00`
  2. Upgrade master components
    - `kubeadm upgrade apply v1.27.0`, for worker nodes: `kubeadm upgrade node`
  3. Upgrade kubelet & kubectl
    - `apt-get install kubelet=1.27.0-00 `
    - `systemctl daemon-reload`
    - `systemctl restart kubelet`
    - `kubectl uncordon NODE`
  4. Upgrade same for worker nodes

### Backup and restore
1. Using declarative way, keeping configuration file in a cloud storage.
2. Using resource config files from kube-apiserver (A must if kubernetes is auto managed)
  - `kubectl get all -all-namespace -o yaml > all-deploy-services-ex.yaml` OR by using tools to manage it
3. Using ETCD server as it has already info about all object in cluster
  - Backup data location are in `etcd.service` in `--data-dir` property
  - `ETCDCTL_API=3 etcdctl snapshot save SNAPSHOT.db` 
  - `ETCDCTL_API=3 etcdctl snapshot status SNAPSHOT.db` see backup status
    - We can `export ETCDCTL_API=3` and use it directly as version 3
    - With `etcdctl` commands specify `--endpoint, --cert, --cacerts, --key` to connect to ETCD server, ex:
      - ```
          --endpoints=https://127.0.0.1:2379 \
          --cert=/etc/kubernetes/pki/etcd/server.crt \
          --cacert=/etc/kubernetes/pki/etcd/ca.crt \
          --key=/etc/kubernetes/pki/etcd/server.key \
        ```
  - `service kube-apiserver stop`
  - Update backup data directory in `etcd.service` in `--data-dir` property, by default is in `/etc/systemd/system/etcd.service` // `/var/lib/etcd`
  - Restart controlplane components, and kubelet if needed `systemctl restart kubelet`
    - `systemctl daemon-reload`
    - `service etcd restart`
  - `service kube-apiserver start`
- `ETCDCTL_API=3 etcdctl --data-dir /DST-DIR snapshot restore SNAPSHOT.db` could specify target dir `--data-dir /DIR`

- `kubectl config use-context CLUSTER` Change cluster context
- `kubectl config view CLUSTER` Change cluster context

## Security
### Authentication Mechanisms
#### TLS
- Certificates: Public Key(`*.crt` or `*.pem`)
- Private Key: (`*.key` or `*-key.pem`)
- We have many tools to manage certificates, we'll use `openssl` **kubadm manages this whole process**
- `openssl genrsa -out FILE.key 2048` Generate key
- `openssl req -new -key MY.key -subj "/CN=CERT-NAME" -out MY.csr` Certificate signing request
  - `"/CN=ISSUED-TO/O=GROUP-DETAILS"` ex: system:masters for admin user privileges.
  - Kubernetes controlplane components must be prefixed with `system` (kube-schedular, kube-controller-manager)
- `openssl x509 -req -in MY.csr -CA CA.crt -CAkey CA.key -out MY.crt` Sign certificate with CA certificate and key
  - `-config openssl.cnf` To add a config file
  - `openssl x509 -req -in CA.csr -signkey CA.key -out CA.crt` Signing CA certificate 
    - `openssl x509 -in CRT.crt -text -noout` Get info about certificate

To Specify more alternative names: @`openssl.cnf`
```
[alt_names]
DNS.1 = ...
....
IP.1 = ....
....
```
Pass this config file as an option while creating the certificate file `-config openssl.cnf`

- Inspect logs:
  - kubeadm is used:
    - `kubectl logs etcd-master`
    - Certificates locations: `/etc/kubernetes/manifests/kube-apiserver.yaml`
  - Configured from scratch, use service logs
  - `journalctl -u etcd.service -l`
    - Certificates locations: `/etc/systemd/system/kube-apiserver.service`
  - If a core component is down
    - `docker ps -a` + `docker logs ID` 

#### Certificates api
By Controller Manager >> CSR-ِAPPROVING & CSR-SIGNING controllers
1. Create CertificateSigningRequest Object
  - `openssl genrsa -out FILE.key 2048` Generate key 
  - `openssl req -new -key MY.key -subj "/CN=CERT-NAME" -out MY.csr` Certificate signing request `*.csr`
  - It's created using manifest file: 
    ```
    apiVersion: certificates.k8s.io/v1
    kind: CertificateSigningRequest
    metadata:
    spec:
      groups:
        - system:authenticated (ex)
      usages:
        - client auth (ex)
      request: 
        {`cat CERT.csr | base64 -w 0` encoded certificate}
    ```
2. View signing requests
  - `kubectl get csr`
  - `kubectl certificate approve CERT-NAME`
    - `kubectl certificate deny CERT-NAME`
3. View certificate
  - `kubectl get csr CERT-NAME -o yaml` base 64 encoded certificate, `echo CERT | base64 --decode` to decode it and share with end user

#### KubeConfig
`$HOME/.kube/config` default location where we define clusters, users, contexts
```
apiVersion: v1
kind: Config

clusters
- cluster:
    certificate-authority: {cert location} OR certificate-authority-data: {encoded cert}
    server:
  name:

current-context: DEFAULT-CONTEXT
contexts:
- context:
    cluster: CLUSTER-NAME
    user: USER-NAME
    namespace: {optional}
  name:

users:
- user:
    client-certificate:
    client-key:
  name: USER-NAME
```
- If it's defined elsewhere, define it explicitly `kubectl get pods --kubeconfig CONFIG-FILE`
- Don't create an object from this file, it's read by kubectl automatically
- `kubectl config view` to view currently running config file
  - `--kubeconfig=CONFIG-FILE`
- `kubectl config use-context CONTEXT` Change context

#### API Groups
- Core group `api` contains all core components
- Named group `apis` contains organized components (Groups > Resources > Verbs)
- `kubectl proxy` launches a local HTTP proxy service on port 8001 to access kube-apiserver, uses credentials and certificates from kubeconfig to access cluster

## Authorization
- Modes
  - Node Authorizer: Authorizes users ex: `system:node:*` & groups `SYSTEM:NODES` like kubelets (Access within cluster)
  - ABAC (Attribute Based Access Control): Associate users or groups with set of permissions through policy files
    - `{kind: "Policy", "spec": {"user": ..., "namespace": ...., "resource": ...., "apiGroup": ....}}`
    - Must be edited manually then restart kube-apiserver
  - RBAC (Role Based Access Control): Create a role with set of permissions, associate users to that role
  - Webhook: Outsource auth process to an external service
  - AlwaysAllow
  - AlwaysDeny
- Specified in the kub-apiserver `--authorization mode=Node,RBAC,...(ex)` default is `AlwaysAllow`

#### RBAC
```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
rules:
- apiGroups: [""] (Empty for core groups)
  resources: ["pods"]
  verbs: ["list", "get", "create", "delete", "watch"]
  resourceNames: [] (Specific pod -resources- names)
```
Or using the imperative way `kubectl create role NAME --verb=...,....,... --resource=....`
#### Role Binding
Link user to role
```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
subjects: (user details)
- kind:
  name:
  apiGroup: rbac.authorization.k8s.io/v1
roleRef: (role details)
  kind:
  name:
  apiGroup: rbac.authorization.k8s.io/v1
```
Or using the imperative way `kubectl create rolebinding NAME --role=... --user=....`
- `kubectl get roles`
- `kubectl get rolebindings`
- `kubectl auth can-i VERB RESOURCE` check current access
  - `--as ANOTHER-USER` check access for another user as an administrator

#### Cluster roles
- Namespaced
  - pod, rs, roles, secrets
- Cluster scoped resources
  - nodes, clusterroles, namespaces
- `kubectl api-resources` Get resources
  - `--namespaced={true,false}` {namespaced, cluster scoped}
- Authorize users in cluster scoped resources by
  - `ClusterRole` (Same as `Role`)
  - `ClusterRoleBinding` (Same as `RoleBinding`)

### Service Accounts
Account used by application to interact with cluster aka `sa`
- In the past serviceAccount is automatically created for each namespace with no expiry date & mounted to it's pods in `/var/run/secrets/kubernetes.io/`
- Since `v1.22` TokenRequestsAPI generates token and and locates it to pods.
- Since `v1.24` we have to create our own Token manually `kubectl create token SA`
- To create a SA with no expiry date, create it and located a special secret to it (see docs -not recommended-)
- Creates a token, stores it in a secret ready to be used or exported to service.
- Locate SA to a specific POD def file under spec: `serviceAccountName: ...`
- `kubectl create serviceaccount NAME` creates service account
- `kubectl create token SVC-ACC-NAME` generate a time bound token for the service account
- `kubectl get serviceaccount`
- Include it in the pod definition file
```
spec:
  serviceAccountName: ....
  automountServiceAccountToken: ... (default to true)
```
#### Image security
registry/user-account/image-repository, registry default is `docker.io`, account default is `library`
- Private repo
  - `docker login NAME`
  - `docker run PRIVATE-REG/.../...`
  - Pass registry credentials to pods
    ```
    kubectl create secret docker-registry SECRET-CRED-NAME
      --docker-server=
      --docker-username=
      --docker-password=
      --docker-email=  
    ```
  - Use registry credentials in pod definition file
    ```
    spec:
      imagePullSecrets:
      - name: SECRET-CRED-NAME
    ```
#### Security Contexts
Define pod or container security settings
```
spec:
  securityContext: (Pod level)
    runAsUser: USERID (Default is root -must be a number-)
  containers:
  - name: ....
    securityContext: (Container level -overrides pod level-)
      runAsUser: USERID
      capabilities: (Only supported at container level)
        add: ["MAC_ADMIN"]
```

### Network Policies
Kubernetes object that limits networking on a pod with source, connection type, port. Supported by **some** kubernetes network solutions.
- `kubectl get networkpolicy` aka `netpol`
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
spec:
  podSelector: (Where the policy applies)
    matchLabels:
      key: value
  policyTypes: (Only specified types are applied, else it's all enabled by default)
  - Ingress {Ingress: allows receiving requests and sending back it's response}
  - Egress
  ingress:
    - from:
        - podSelector: 
            matchLabels:
              name:
          namespaceSelector: (As it's not a separate rule defined by '-', it applies as AND to the previous rule)
            matchLabels: 
              name:
      ports:
        - protocol: TCP
          port:

    - from
        - ipBlock: (allow external IP to policy)
            cidr: EXTERNAL-IP
      ports:
        - protocol: TCP
          port:

  egress:
    ....
```

## Storage
- Declare storage in the pod definition file:
```
spec:
  volumes:
    - name: NAME
      hostPath:
        path: /HOST-PATH
  containers:
    - image:
      volumeMounts:
        - mountPath: /CONT-PATH
          name: VOLUME-NAME
```

### Persistent volumes (PV)
A cluster wide pool of storage volumes to be used by users deploying apps to cluster, to manage storage centrally. aka `pv`
- Created by Administrators
```
apiVersion: v1
kind: PersistentVolume
metadata:
spec:
  persistentVolumeReclaimPolicy: {Retain: keep pv but not available, Delete: deletes pv, Recycle: Scrub, keep pv}(After PVC deletion)
  accessModes:
    - {ReadOnlyMany, ReadWriteOnce, ReadWriteMany}
  capacity:
    storage: 1Gi
  hostPath: (Replaced by any storage solution)
    path: /...
```
### Persistent Volume Claim (PVC)
To make storage available to a node. Maps to only a single persistent volume **1PVC: 1PV** aka `pvc`
- Created by Users
- `kubectl get persistentvolumeclaim`
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
spec:
  accessModes:
    - {ReadOnlyMany, ReadWriteOnce, ReadWriteMany}
  resources:
    requests:
      storage: 500Mi
  volumeMode: MODE
  storageClassName: SC-NAME
```
- Use the created PVC in the *pod* definition file under the volumes section:
```
spec:
  volumes:
    - name: NAME (To be used in volumeMounts name)
      persistentVolumeClaim:
        claimName: CLAIM-NAME
```
WHAT'S THE USE OF FIRST TYPE STORAGE
### Storage Class
Defines provisioner which automatically creates PV, attaches it to pods -**Dynamic Provisioning**- aka `sc`
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: CLASS-NAME
provisioner: kubernetes.io/gce-pd (ex)
parameters:
  type: {pd-standard, pd-ssd}
  replication-type: {none, regional-pd}
```
- Use it in PVC
```
spec:
  storageClassName: CLASS-NAME
```

## Networking
- `ip link` list, modify *interfaces* on a host
  - `show INTERFACE` show addresses of interface
- `ip addr` see IP addresses assigned to these interfaces
  - `show INTERFACE` show addresses of interface
- `ip addr add ADDRESS-IN-NETWORK dev INTERFACE` assign IP-addresses to interfaces
- `ip address show type bridge` view specific network type
- `netstat` displays all network connections (use set of desired options)

- `ip route` manage current routing configuration -routing table-
- `ip route add TRGT-ADDR via GATEWAY-ADDR` add entry to routing table, for default route, set TRGT to `default`
- `/proc/sys/net/ipv4/ip_forward` write `1` to enable forwarding packets between private and public interfaces, `0` to deny (default)
- `iptables -t nat -A PREROUTING --dport PORT --to-destination DST-IP -j DNAT` create an IP forwarding rule for incoming request on that port
- `/etc/sysctl.conf` Persist any change  
  - `net.ipv4.ip_forward = 1` to persist IP forwarding
- Name resolution
- `/etc/hosts` Modify Name Resolution: `192.168.1.1    host1`
- `/etc/resolv.conf` Configure hosts to point to DNS >> `nameserver IP`
- `/etc/nsswitch.conf` To prioritize hosts file or dns in case of common addresses, order them.
- `nslookup HOST-NAME` query a hostname from DNS only
- `dig HOST-NAME` test DNS resolution with more details
- Namespace
- `ip netns` list network namespaces
  - `add NAME-SPACE`
  - `exec NAME-SPACE CMD` execute command within a namespace or `ip -n NS CMD`
- Allow internal namespaces communication (Handled by )
  1. We could imitate a cable and connection -pipe- at both namespaces with `veth`
  2. Create an private network using virtual switch (Bridge interface), connect namespaces & wires to it (Interface for hosts, switch for namespaces)
- Docker
  - `docker run --network`
    - `none` container is isolated from any connection
    - `host` container uses same network as host, no isolation(container port is mapped to host port)
    - `bridge` (Default) Usually @172.17.0.1
      - as if it ran `ip link add docker0 type bridge` where docker0 is the real name it uses on host, test by `ip link`
  - `docker network ls`
  - Docker creates a container > creates a namespace > pair of interfaces > Attaches one end to container, the other to the bridge network

#### Container Networking Interface (CNI)
Unified set of responsibilities for container runtime and plugins to dynamically configure network resources.
- Networking solutions handles pods networking problems
- `/opt/cni/bin` all supported Network *plugins*
- `/etc/cni/net.d` has the *configuration* file to be used by kubelet.
- `kubelet.service` >> `--network-plugin=cni` to specify Network plugin, view it by `ps -aux | grep kubelet`

- weaveworks (Networking solution)
  - `kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml` install weave
  - Installs it's agents on each node as a service which stores a topology of the entire cluster setup.
  - Creates it's own bridge on nodes named `WEAVE`
  - Weave agent intercepts sent packets to pods on another nodes, encapsulates it with new src & dst, the other agent retrieves it, decapsulate it, routes it to the right pod
- IP Address Management (IPAM)
  - Weave assigns IPs within range of 10.32.0.0/12 ~ 1M Addresses
  - To get PODS IP addresses range: See **weave pod logs** `ipalloc-range:`
  - To get Services IP addresses range: See **kube-apiserver** creation file

### Service Networking
Services cluster wide -accessible anywhere from the cluster- managed by `kube-proxy` as a *DaemonSet* to be deployed on each node in the cluster.
- `kube-proxy --proxy-mode {userspace, iptables, ipvs}` select mode to manage rules (Default is `iptables`)
- `iptables -L -t nat | grep SVC` see rules created by kube-proxy for each service OR `/var/log/kube-proxy.log` kube-proxy logs.

### DNS in Kubernetes
Fully Qualified Domain Name (FQDN) `hostname.namespace.type.root` Default root domain: cluster.local
- Kubernetes now uses CoreDNS
  - Is deployed by a Deploymnet with 2 instances for replication. 
  - runs `./Coredns` executable 
  - `/etc/coredns/Corefile` configuration file, deployed as a ConfigMap object, we can edit it directly from there.
  - `kubelet` manages setting the pods dns nameserver to the dns service
  - We have to explicitly tell it to use PODS records in the ConfigMap
  - `nslookup SVC` or `host SVC` to manually lookup DNS, returns FDQN
  - When specifying PODS use the FQDN
### Ingress
Helps users access application using a signle accessible URL configured to route traffic to services within cluster based on the URL path & at the same time implement SSL security
#### 1. Ingress Controller
**Doesn't come with kuberentes by default** we have to deploy one ex: GCE, nginx
- Deployment: Of Ingress image (nginx)
- Service: To expose it
- ConfigMap: Feed configuration data to Service
- ServiceAccount: To access all of these objects with the right permissions (Auth)
#### 2. Ingress Resources
Configurations applied on the ingress controller
- *Rules* for host & domain name. *Paths* within rules to route traffic based on URL.
- `kubectl get ingress`
- `kubectl create ingress NAME --rule="DOMAIN.com/PATH*=SERVICE:PORT"` create imperatively
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.rngress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - host: HOST-NAME-OR-DOMAIN (Only in case of many Domain names)
    http:
      paths:
      - path: /PATH
        backend:
          service:
            name: NAME
            port:
              number: 
```

## Design and Install Kubernetes
- Minikube: Provisions a VM for a single node cluster
- kubeadm: Requires VMs to be ready, allows multi-node cluster
#### Infrastructure
- Turnkey solutions: You manage everything privately on premises, ex: KOPS on AWS & OpenShift
- Hosted solutions: Provider manages everything, ex: Google Container Engine (GKE) & AWS EKS & OpenShift online.
### Hight Availability
- API Server:
  - Works in Acitve-Active mode in case of many masters
  - Needs a load balancer on top of them
- Controller Manager, Scheduler
  - Works in Acitve-StandBy mode 
  - `kube-controller-manager --leader-elect BOOL [opts]` By default, it tries to gain lock on kube-controller-manager Endpoint
- ETCD
  - Stacked topology: Runs on master nodes, easier setup, manage, fewer servers, risk of failures
  - External topology: Runs on separate node, less risky, harder to setup, more servers
    - ETCD is distributed: call any server of them directly
  - Leader Selection: RAFT protocol
    - Write opereation is done via the leader only and forwarded to him
    - Cluster is up if at least a Qourum number of servers is up **floor(N/2 + 1)**. Odd number of node >= 3 is more efficient for fault tolerance.

## Install kubernetes using kubeadm
1. Provision nodes
2. Install containerd CR
3. [Install kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
  - ex:
    ```
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl

    mkdir -p /etc/apt/keyrings

    curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg

    echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

    sudo apt-get update
    sudo apt-get install -y kubelet=1.27.0-00 kubeadm=1.27.0-00 kubectl=1.27.0-00
    sudo apt-mark hold kubelet kubeadm kubectl
    ```
    Run output given commands to create kubeconfig file
4. [Initialize the master server](https://v1-27.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
  `kubeadm init [args]`
5. Setup POD network
6. Woker nodes join master node
  - With the provided output of `kubeadm init`
  - ex:
    `kubeadm join 192.25.74.3:6443 --token dho3dd.3sxa2q71zjupudmw --discovery-token-ca-cert-hash sha256:af68f6a7e3cc75aeebb33aa3f24c835b251384ad4116b027c4629fd932ade222`

## Troubleshooting
### Appliation Failure
  - Check Accessibility
    - Start from either ends depending on failure.
  - Check if ip is accessible using `curl`
  - Check service if it's discovered the endpoints based on selectors if they match
  - Check pod state, restarts, events, logs `-f` watch in real time OR `--previous` for previous pod logs.
### Control Plane Failure
  - Check nodes status
  - Check pods status
  - Check controlplane pods & services
  - Check controlplane components logs
    - `sudo journalctl -u kube-apiserver` If services are configured natively on master node
### Worker Node Failure
  - Check Node status
    - Check memory and disk space on the nodes `top` & `df -h`
  - Check Kubelet status
    - Check kubelet service `/var/lib/kubelet/CONFIG-FILE` OR kubelet configuration file or `/etc/kubernetes/kubelet.conf`
    - Check status `service kubelet status`, if it's inactive `service kubelet start`
    - Check logs `sudo journalctl -u kubelet` 
  - Check certificates `openssl x509 -in /var/lib/kubelet/WORKER-NODE -text`
    - Ensure it's not expired, right group, Issed by right CA.

## JSON Path
kube-apiserver return full info in json format
### JSON PATH in kubectl
1. Identify the kubectl command
2. Familiarize with JSON output `-o json`
3. From the JSON PATH query
4. Use the JSON PATH query with the kubectl command
  - `-o=jsonpath='{ json } {"\n"} {"\t"} ...'`
### Loops
- `'{.PROP[*].PROP.PROP}{"\n"}{SECOND COLUMN}'`
### Custom columns
- `-o=custom-columns=CUSTOM-NAME:FIELD, .....`
- `--sort-by=FIELD`