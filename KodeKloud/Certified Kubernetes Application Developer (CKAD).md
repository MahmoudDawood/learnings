# Certified Kubernetes Application Developer (CKAD)
- `kubectl scale --replicas=N DEP/SVC DEP/SVC-NAME` Scale rs (Not updated in OG definition file)
- `kubectl config set-context $(kubectl config current-context) --namespace=NS` To specify the default Namespace
  - `--namespace == -n`
- `expose pod redis --port=6379 --name redis-service` create clusterIP service (Default type)
- `create service clusterip redis --tcp=6379:6379` same
- `expose pod nginx --port=80 --name nginx-service --type=NodePort` create NodePort service
- `create service nodeport nginx --tcp=80:80 --node-port=30080` same
- `kubectl run httpd --image=httpd:alpine --port=80 --expose` create a pod and it's service with the same name <<


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