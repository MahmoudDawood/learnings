# CKA Certification Course – Certified Kubernetes Administrator

### Cluster Architecture
- Master: Manage, plan, schedule, monitor Nodes
- Worker Nodes: Hosts application as container

#### Master
- ETCD Cluster: DB stores info about nodes in a key value format (DB)
- kube-scheduler: Schedules the right node to place a container. (Crane)
- Controllers: (Control rooms)
  - Node-Controller: On boards nodes, handles their issues
  - Replication-Controller: Ensures desired number of containers is running at all times
- kube-apiserver: Primary management component to orchestrate all operations in a cluster (Master)
Worker Nodes:
- kubelet: Agent runs on each node, listens to instructions from kube-apiserver, sends reports to it
- kubeproxy: Enables communication between services on a cluster
ADD IMAGE