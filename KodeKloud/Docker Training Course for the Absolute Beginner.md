# Docker Training Course for the Absolute Beginner
- `docker`
  - `version`
  - `login`
  - `pull`
  - `build SRC -t ACCOUNT/IMAGE-NAME` Create the image **Must be logged in first `docker login`**
  - `push ACCOUNT/IMAGE-NAME` Push image to docker hub **Must have a tag name**
  - `run` form `hub.docker.com` *Pulls it if image not found*
    - `--name NAME`
    - `-u USER` 
    - `IMAGE:VERSION` specify a version or a tag
    - `-it` interactive + pseudo terminal
    - `-d` detached mode
    - `-p HOST-PORT:CONTAINER-PORT` mapping port
    - `-v HOST-VOL:CONTAINER-VOL` mapping volumes
    - `-e ENV-VAR=VALUE` setting environment variables
    - `--link CONTAINER-NAME:HOST-NAME` Links two containers together **A container name must be used**, creates an entry for it in the `/etc/hosts` file
    - `--network NETWORK` specify a network other than bridge 
    - `--cpus=.5` Limit container cpu usage to 50%
    - `--memory=100m` Limits container memory usage to 100MB
  - `attach CONTAINER` move container to foreground
  - `ps` list running containers
    - `-a` list all containers
  - `stop`
  - `rm` with name or unique enough id (ex: first 3 chars) **Stop container before removing it**
    - **`$(docker images -aq)`** remove all images
  - `images` lists available images
  - `docker image prune` remove all local dangling images
    - `-a` and remove all images not being used by a container
  - `inspect` *more info on a docker object*
  - `rmi` remove an image **Delete it's using containers first**
  - `exec CONTAINER COMMAND` execute a command on a container
  - `docker-compose up` runs the below `docker-compose.yml` file
  - `network create --driver bridge  --subnet nn.nn.nn.nn/nn NETWORK-NAME`
  - `network ls`
## Creating an image
Docker file consists of [Instruction Argument], uses *caching* and *layered architecture*
```
FROM base-image
RUN command
COPY src dst
EXPOSE port
ENTRYPOINT command-to-run-app (gets appended to the end of command vs CMD runs after the command is finished)
```
ENTRYPOINT and CMD can be combined together to use default values, but a json array format must be used.
- `docker build SRC -t ACCOUNT/IMAGE-NAME` Create the image **Must be logged in first `docker login`**
- `docker push ACCOUNT/IMAGE-NAME` Push image to docker hub **Must have a tag name**

## Docker Compose
Used to set a complex application running multiple services *on a single host*
### Install docker-compose
`docs.docker.com/compose/install`
- `docker-compose up` runs the below `docker-compose.yml` file
```
version: "n" (@n >= 2 up to 3)
services: (@n >= 2) container-name
  image: image-name (OR build: ./dir-to-build-then-use)
  ports:
    - port:port
  environment:
    - var: var
  links: (@n == 1)
    - a-container-name networks: (@n >= 2) 
  - n1 networks: (@n >= 2)
    n1: n2: 
```
## Engine 
- `docker -H=REMOTE-IP` Use docker cli to manage a remote docker engine 
### File system 
`/var/lib/docker` 
- To edit a part of the image, it uses *copy on write* which make a copy in container, edits and uses it.
#### Volume mounting
  - `docker volume create .....` Creates a docker volume in `/var/libs/docker/volumes`
  - `docker run -v VOLUME-NAME:CONTAINER-DIR`
#### Bind mounting 
  - `docker run -v LOCAL-DIR:CONTAINER-DIR`
  - `docker run --mount type=bind,source=/LOCAL-DIR,target=/CONTAINER-DIR IMG` *Preferred verbose way*

## Networking
1. Bridge: (Default) Usually @172.17.0.1
2. none: Isolated from any network
3. Host: container uses same network as host, no isolation (A container port is used only once mapped to host port)
- `docker run --network=` specify a network other than bridge
- `docker network create --driver bridge  --subnet nn.nn.nn.nn/nn NETWORK-NAME`
- `docker network ls`
- `docker inspect bridge`

#### Embedded DNS @127.0.0.11 
Helps containers connect with names

## Registry
- A system for versioning, storing and distributing Docker images
- registry/user-account/image-repository
- `docker.io` default docker registry
#### Use private registry
  - `docker run private-reg/acc/image` **Login to private registry before using it `docker login`**

### Deploy private registries
Registry can be treated as a container to use locally ex: In an enterprise company
- `docker run -d -p 5000:5000 --name registry registry:2` run docker registry as a container to use it
- `docker image tag IMG localhost:5000/IMG` create a target image referring to the original image
- `docker push localhost:5000/IMG`
- `docker pull localhost:5000/IMG`

## Windows containers
To use a linux container on windows
1. Docker toolbox ex: Oracle Virtualbox (must be windows 7 or later)
2. Desktop application using Hyper-V (HyperKit in mac)
- Both options cannot coexist

## Container orchestration 
1. Docker swarm
  - consists of a master swarm manager and worker nodes (both are called a cluster)
  - `docker swarm init` initialize swarm manager
  - `docker service create --replicas=n img-name` create service(distributed container) on nodes across a cluster(group of nodes)
2. Kubernetes
  Uses kubectl (Kubernetes cli tool)
  - `kubectl run img`
  - `kubectl cluster-info`
  - `kubectl get nodes`
  - `kubectl run my-web-app --image=my-web-app --replicas=100`