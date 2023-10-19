# Docker Training Course for the Absolute Beginner

 - `version`
 - `pull`
  - `run` form `hub.docker.com` *Pulls it if image not found*
    - `--name NAME`
    - `-it` interactive + pseudo terminal
    - `-d` detached mode
  - `ps` list running containers
    - `-a` list all containers
  - `stop`
  - `rm` with name or unique enough id (ex: first 3 chars)
    - **`$(docker images -aq)`** remove all images
  - `images`
  - `rmi` remove an image **Delete it's using containers first**
  - `exec CONTAINER COMMAND`