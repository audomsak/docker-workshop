# Docker Demo

## Docker Hub

___

- Open Docker Hub [website](https://hub.docker.com/).

- Quick tour, Official images, Tags, Dockerfile, and manual.

## Basic container usage

___

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

- Pull image from [quay.io](quay.io) to demostrate that Docker can also pull image from other repositories.

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

### - Run container with and without local image

- Run a container with a pulled image.

  ```sh
  docker run nginx:latest
  # OR
  docker container run nginx:latest
  ```

- Run a container and name it. The container name can be used as a reference with other commands.

  ```sh
  docker run --name nginx nginx:latest
  # OR
  docker container run --name nginx nginx:latest
  ```

- Run a container without pulling container image. Any container can be run without pulling the image manually; Docker will be finding the image in local first, if the image doesn't exist then it will try to pull the image from image repository (Docker Hub).

  ```sh
  docker container run httpd
  ```

### - Inspect inside the running container

- Get an interactive terminal for a container using a command below. Then use `ls` command to explore file system hierarchy inside the container.

  ```sh
  docker exec -it <container ID or name> /bin/sh
  # OR
  docker container exec -it <container ID or name> /bin/sh
  ```

### - Running container with interactive mode, without interactive mode, and detatch mode

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

### - Attach to container running in detatch mode

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

___

### - Docker annonymous volume

- Run a container that uses volume.

  ```sh
  docker container run -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb --name postgresdb postgres
  ```

- Inspect the volume using a command below. Then look for `"Mounts"`. And then open a file explorer to demostrate that Docker has create an annonymous volume automatically.

  ```sh
  docker inspect <container ID or name>
  # OR
  docker container inspect <container ID or name>
  ```

### - Docker named-volume

- Run a container with a named-volume.

  ```sh
  docker container run -d -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb -v pgdata:/var/lib/postgresql/data --name postgres postgres
  ```

- Inspect the volume using a command below. Then look for `"Mounts"`. And then open a file explorer to demostrate that Docker would create an annonymous volume automatically.

  ```sh
  docker inspect <container ID or name>
  # OR
  docker container inspect <container ID or name>
  ```

### - Docker bind mount

- Run a container with bind mount.

  ```sh
  docker run -d -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb -v ~/temp/docker/pgdata:/var/lib/postgresql/data --name postgres postgres
  ```

- Inspect the volume using a command below. Then look for `"Mounts"`. And then open a file explorer to show the audiences that the mount get binded automatically.

  ```sh
  docker inspect <container ID or name>
  # OR
  docker container inspect <container ID or name>
  ```

### - Docker volume management

- Get list of volumes

  ```sh
  docker volume ls
  ```

- Remove a volume

  ```sh
  docker volume rm <volume name>
  ```

- Remove all unused volumes

  ```sh
  docker volume prune
  ```

## Container Network: Docker bridge, host and none networks

___

### - Bridge network mode demostration

- Run a container with the `bridge` network mode.

  ```sh
  docker container run -d --network bridge --name nginx nginx
  ```

- Open a website with host's web browser to demostrate that a container is running in Docker private network hence can't be accessed from outside of `bridge` network.

- Inspect the container to get IP address using the command below. Then look for `IPAddress` in the `"Networks"` section.

  ```sh
  docker container inspect <container ID or name>
  ```

- Run `wget` command in a new container to demostrate that the containers within the same `bridge` network can access to each other.

  ```sh
  docker container run --network bridge busybox wget -S -O- http://<IP Address>
  ```

### - Expose a container to outside of `bridge` network

- Run a container with `bridge` network mode and port forwarding.

  ```sh
  docker container run -d --network bridge -p 8080:80 --name nginx nginx
  ```

- Open a website with host's web browser to demostrate that a container is running in Docker private network with port forwarding can be accessed from outside of `bridge` network.

- Run `wget` command in a new container to demostrate that the containers within the same `bridge` network can access to each other.

  ```sh
  docker container run --network bridge busybox wget -S -O- http://<IP Address>
  ```

### - Host network mode demostration

- Run a container with `host` network mode without port forwarding.

  ```sh
  docker container run -d --network host --name nginx nginx
  ```

- Open a website with host's web browser to demostrate that a container running with Docker `host` network can be accessed from the host's network.

- Inspect the container to get IP address using the command below. Then look for `IPAddress` in the `"Networks"` section.

  ```sh
  docker container inspect <container ID or name>
  ```

- Run `wget` command in a new container to demostrate that the container running in `bridge` network can also access to the container running in the `host` network.

  ```sh
  docker container run --network bridge busybox wget -S -O- http://<IP Address>
  ```

### - User-defined `bridge` network

- User can define new `bridge` networks to isolate container(s) from other containers with different network space. The user-defined `bridge` network also allow containers within the network to communicate to each other using both IP Address and container name while the default `bridge` network allows the communication using IP Address only.

- Create a new `bridge` network.

  ```sh
  docker network create mybridge
  ```

- Get list of network

  ```sh
  docker network ls
  ```

- Run a container with the `mybridge` network without port forwarding.

  ```sh
  docker container run -d --network mybridge --name nginx nginx
  ```

- Inspect the container to get IP address using the command below. Then look for `IPAddress` in the `"Networks"` section.

  ```sh
  docker container inspect nginx
  ```

- Start a `busybox` container with the `mybridge` network and interactive mode.

  ```sh
  docker container run -it --network mybridge busybox sh
  ```

- Ping to `nginx` container using container name

  ```sh
  ping nginx
  ```

- Ping to `nginx` container using IP Address.

  ```sh
  ping <IP Address>
  ```

- Try to get a home page from `nginx` container.

  ```sh
  wget -S -O- http://nginx
  ```

## Container Image management

___

### - Building container image

- Clone a project from GitHub.

  ```sh
  git clone https://github.com/audomsak/node-express-website
  ```

- Enter to the `node-express-website` directory.

- View `Dockerfile` detail.

- Build a container image without specifying repository and tag.

  ```sh
  docker image build .
  ```

- Get list of images to demostarate that building a container image without repository and tag is hard to remember because Docker generated only image ID for the container image.

  ```sh
  docker image ls
  ```

- Build a new container image and specify repository but not tag.

  ```sh
  docker image build -t audomsak/node-website .
  ```

- Get list of images to demostarate that building a container image and specify repository without tag, Docker would tag the container image with `latest` tag automatically.

  ```sh
  docker image ls
  ```

- Build a new container image and specify repository and tag.

  ```sh
  docker image build -t audomsak/node-website:1.0 .
  ```

- Get list of images to demostarate that building a container image and specify repository and tag, Docker would tag the container image with the specified tag.

  ```sh
  docker image ls
  ```

- Run a container with the image we just built.

  ```sh
  docker container run -d -p 8080:80 --name website audomsak/node-website:1.0
  ```

### - Tagging and versioning

- Add new tag for the existing container image to have multiple tags or versions.

  ```sh
  docker image tag audomsak/node-website:1.0 audomsak/node-website:release
  ```

- Get list of images to demostarate a new tag gets created.

  ```sh
  docker image ls
  ```

### - Pushing container image to Docker Hub

- Login to Docker hub using the command below then enter username and password.

  ```sh
  docker login
  ```

- Push a container image to Docker hub

  ```sh
  docker image push audomsak/node-website:release
  ```

- Go to Docker hub [website](https://hub.docker.com/) to verify the image.

## Administration

___

### - Clean up containers, images and volumes

- Clean up containers

  ```sh
  docker container prune
  ```

- Clean up images

  ```sh
  docker image prune
  ```

- Clean up volumes

  ```sh
  docker volume prune
  ```

- Clean up containers, networks, images, and build cache

  ```sh
  docker system prune
  ```

### - Check disk usage

- Check how many disk space used by Docker

  ```sh
  docker system df
  ```

___

