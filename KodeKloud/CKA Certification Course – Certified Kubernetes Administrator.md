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
- `kubectl get configmaps`
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
- `get rolebindings` + all other operations
- `auth can-i VERB RESOURCE` check current access
- `--as ANOTHER-USER` Administrator makes action as a user
- `api-resources` Get resources
  - `--namespaced={true,false}` {namespaced, cluster scoped}
- `get serviceaccount`

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
        cpu: 2 (1m (milli cores) - ....)
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
                      name: CONFIG-NAME
                      key: KEY
```
#### ConfigMaps
Manage configuration data centrally.
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
Store sensitive information in an *encoded* format, (Same as ConfigMap)
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
          secretKeyRef: 
            name: SECRET-NAME
- `echo -n 'secret' | base64` to encode secret before storing it in file
- `kubectl get secret NAME -o yaml` to view the secret values
- `echo -n 'secret' | base64 --decode` to decode a secret 
- Notes
  - Secrets are encoded NOT encrypted
  - Secrets are not encrypted in ETCD, we have to enable encryption at rest
  - Secrets are available to anyone who can create pods/deployments in namespace
    - Consider Role Based Access Control (RBAC)
  - Consider 3rd party secret providers 

### Enable Encryption at rest
#### View secrets from etcd
```
ETCDCTL_API=3 etcdctl \
   --cacert=/etc/kubernetes/pki/etcd/ca.crt   \
   --cert=/etc/kubernetes/pki/etcd/server.crt \
   --key=/etc/kubernetes/pki/etcd/server.key  \
   get /registry/secrets/default/SECRET-TO-SEE | hexdump -C
```
- Create `EncryptionConfiguration` object file, pass it as an option
```
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
      - configmaps
    providers:
      - aescbc:
          keys:
            - name: key1
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
- `kubectl get secrets --all-namespaces -o json | kubectl replace -f -` Ensure all old data are encrypted

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
king: Config

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
    runAsUser: USERID
  containers:
  - name: ....
    securityContext: (Container level -overrides pod level-)
      runAsUser: USERID
      capabilities: (Only supported at container level)
        add: ["MAC_ADMIN"]
```