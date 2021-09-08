# Docker Hands-on Lab

## Basic container usage

### - Pull container image form Docker Hub

- Pull image without specifying a tag. The image with `latest` tag will get pulled.

  ```sh
  docker pull nginx
  # OR
  docker image pull nginx
  ```

- Pull image with a specific tag.

  ```sh
  docker pull nginx:1.21
  # OR
  docker image pull nginx:1.21
  ```

### - Pull container image from non-Docker Hub image repository

- Docker can also pull image from other private and public repositories. For example, pull image from [quay.io](quay.io).

  ```sh
  docker image pull quay.io/public/openjdk:8-slim
  ```

### - Get list of container images in local

- Get list of container images.

  ```sh
  docker images
  # OR
  docker image ls
  ```

### - Run a container with container image in local

- Pull image from Docker Hub

  ```sh
    docker pull nginx:1.21
    # OR
    docker image pull nginx:1.21
    ```

- Run a container with the pulled image.

  ```sh
  docker run nginx:1.21
  # OR
  docker container run nginx:1.21
  ```

### - Run a container without container image in local

- Run a container without pulling container image. Any container can be run without pulling the image manually; Docker will be finding the image in local first, if the image doesn't exist then it will try to pull the image from image repository (Docker Hub).

  ```sh
  docker container run httpd
  ```

### - Run a container and give it a name

- Run a container and name it. The container name can be used as a reference with other commands.

  ```sh
  docker run --name nginx nginx:1.21
  # OR
  docker container run --name nginx nginx:1.21
  ```

### - Run a container and override its default command

- Override with a single command

  ```sh
  docker run nginx:1.21 ls -la
  # OR
  docker container run nginx:1.21 ls -la
  ```

### - Running a container with interactive mode, without interactive mode, and detatch mode

- Run a container with interactive mode.

  ```sh
  docker run -it ubuntu
  # OR
  docker container run -it ubuntu
  ```

- Run a container without interactive mode.

  ```sh
  docker run ubuntu
  # OR
  docker container run ubuntu
  ```

- Run a container with detatch mode.

  ```sh
  docker run -d nginx
  # OR
  docker container run -d nginx
  ```

### - Get list of containers

- Get list of `running` containers.

  ```sh
  docker ps
  # OR
  docker container ps
  ```

- Get list of all containers regardless of their statuses.

  ```sh
  docker ps -a
  # OR
  docker container ps -a
  ```

### - Attach to a container running in detatch mode

- Attach local standard input, output, and error streams to a running container.

  ```sh
  docker attach <container ID or name>
  # OR
  docker container attach <container ID or name>
  ```

### - Execute command inside a container from outside (host)

- Run a command in a running container

  ```sh
  docker exec <container ID or name> <command>
  # OR
  docker container exec <container ID or name> <command>
  ```

### - Start, Stop, and Delete an existing container

- Start a stopped container.

  ```sh
  docker start <container ID or name>
  # OR
  docker container start <container ID or name>
  ```

- Stop a running container.

  ```sh
  docker stop <container ID or name>
  # OR
  docker container stop <container ID or name>
  ```

- Delete a container.

  ```sh
  docker rm <container ID or name>
  # OR
  docker container rm <container ID or name>
  ```

### - Inspect a container

- Inspect a container to see its details.

  ```sh
  docker inspect <container ID or name>
  # OR
  docker container inspect <container ID or name>
  ```

### - Inspect inside a running container

- Get an interactive terminal for a container using the command below. Then use `ls` command to explore file system hierarchy inside the container.

  ```sh
  docker exec -it <container ID or name> /bin/sh
  # OR
  docker container exec -it <container ID or name> /bin/sh
  ```

### - Inspect container logs

- Get all logs from a container.

  ```sh
  docker logs <container ID or name>
  # OR
  docker container logs <container ID or name>
  ```

- Keep following logs from a container.

  ```sh
  docker logs -f <container ID or name>
  # OR
  docker container logs -f <container ID or name>
  ```

### Container Storage: Docker Volume and Bind mount

### - Docker Volume: named-volume

- Run a container with a named-volume.

  ```sh
  docker container run -d -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb -v pgdata:/var/lib/postgresql/data --name postgresdb2 postgres
  ```

- Inspect the container to get volume information using the command below. Then look for `"Mounts"`.

  ```sh
  docker inspect postgresdb2
  # OR
  docker container inspect postgresdb2
  ```

- Verify that the `pgdata` volume gets created.

  ```sh
  docker volume ls
  ```

- (Optional) Use `ls` command or a file explorer to explore data inside the volume.

### - Docker Bind mount

- Run a container with bind mount.

  ```sh
  docker run -d -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb -v $HOME/docker/pgdata:/var/lib/postgresql/data --name postgresdb3 postgres
  ```

- Inspect the container to get volume information using the command below. Then look for `"Mounts"`.

  ```sh
  docker inspect postgresdb3
  # OR
  docker container inspect postgresdb3
  ```

- Use `ls` command or a file explorer to explore data inside the `$HOME/docker/pgdata` directory.

## Container Network

### - Expose a service running in container to outside of `bridge` network (Default network)

- Run a container with `bridge` network mode and port forwarding.

  ```sh
  docker container run -d --network bridge -p 8080:80 --name nginx nginx
  ```

- Open a website with host's web browser to verify that the container running in Docker private `bridge` network with port forwarding can be accessed from outside of `bridge` network.

- Inspect the container to get IP address using the command below. Then look for `IPAddress` in the `"Networks"` section.

  ```sh
  docker container inspect nginx
  ```

- Run a new container and execute `wget` command to verify that the containers within the same `bridge` network can access to each other.

  ```sh
  docker container run --network bridge busybox wget -S -O- http://<IP Address of another container>
  ```

## Container Image management

### - Building container image

- Clone a project from GitHub.

  ```sh
  git clone https://github.com/audomsak/node-express-website
  ```

- Enter to the `node-express-website` directory.

- Verify `Dockerfile` details.

- Build a new container image and specify repository and tag.

  ```sh
  docker image build -t <your Docker Hub account>/node-website:1.0 .
  ```

- Get list of images to verify that building a container image and specified repository and tag, Docker would tag the container image with the specified tag.

  ```sh
  docker image ls
  ```

- Run a container with the image we just built.

  ```sh
  docker container run -d -p 8080:80 --name mywebsite <your Docker Hub account>/node-website:1.0
  ```

### - Tagging and versioning

- Add a new tag for the existing container image to have multiple tags or versions.

  ```sh
  docker image tag <your Docker Hub account>/node-website:1.0 <your Docker Hub account>/node-website:latest
  ```

- Get list of container images to verify the new tag gets created.

  ```sh
  docker image ls
  ```

### - Pushing container image to Docker Hub

- Login to Docker hub using the command below then enter Docker Hub username and password.

  ```sh
  docker login
  ```

- Push a container image to Docker hub

  ```sh
  docker image push <your account>/node-website:1.0
  docker image push <your account>/node-website:latest
  ```

- Go to Docker Hub [website](https://hub.docker.com/) to verify the image.

___

