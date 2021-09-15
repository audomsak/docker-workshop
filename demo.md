# Docker Demo

## Container Usages

<details>

<summary>Lab details</summary>

___

### - Pull container image form Docker Hub

- Pulling a NGINX container image *without* specifying a tag. Docker will pull the image with `latest` tag.

  ```sh
  docker image pull nginx
  ```

- Pull container image with a specific tag by adding `:` after the image name followed by a tag name.

  ```sh
  docker image pull nginx:1.21
  ```

___

### - Pull container image from other registries

- Pulling an Open JDK 8 container image from [quay.io](quay.io).

  ```sh
  docker image pull quay.io/public/openjdk:8-slim
  ```

___

### - Get list of container images

- Get list of container images on the host.

  ```sh
  docker image ls
  ```

___

### - Run a container with container image in local

- Run a container with the image you just pulled above.

  ```sh
  docker container run nginx:1.21
  ```

- Press `Ctrl + C` on keyboard to stop and exit from the container.

___

### - Run a container without container image in local

- Run a container without pulling container image.

  ```sh
  docker container run httpd
  ```

- Press `Ctrl + C` on keyboard to stop and exit from the container.

___

### Run a container in detatch mode

- Run NGINX container in detatch mode by adding `-d` flag. The container will be running in background like daemon processes.

  ```sh
  docker container run -d --name nginx-daemon nginx
  ```

  Docker will print out only container ID and you won't see any output or logs.

- Use following command to check if the container is running.

  ```sh
  docker container ls
  ```

___

### - Get list of containers

- Get list of `running` containers.

  ```sh
  docker container ps
  ```

- Get list of all containers regardless of their statuses.

  ```sh
  docker container ps -a
  ```

___

### - Attach to a container running in detatch mode

- Run an Ubuntu container in detatch mode (`-d`) and name the container (`--name`) as `ubuntu-date` and override its default command (`bash`) with an inline while loop that will be printing current date and time every second.

  ```sh
  docker run -d --name ubuntu-date ubuntu sh -c 'while true; do date; sleep 1; done'
  ```

  Docker will print out only container ID and you won't see date and time get printed out because the container will be running as a background process.

- Attach local standard input, output, and error (your terminal) to the container.

  ```sh
  docker container attach ubuntu-date
  ```

  You'll see date and time get printed out for every second.

- Press `Ctrl + C` on keyboard to stop and exit from the container.

___

### - Execute command inside a container from host

- First, run a container in detatch mode.

    ```sh
  docker container run -d --name mynginx nginx:1.21
  ```

- Run `env` command inside `mynginx` container.

  ```sh
  docker container exec mynginx env
  ```

___

### - Inspect a container

You can inspect the details of any container to see its details i.e. Network, Mount, Configurations and so on.

- Inspect `mynginx` container you ran earlier to see its details.

  ```sh
  docker container inspect mynginx
  ```

___

### - Inspect inside a running container

- Get an interactive terminal for `mynginx` container.

  ```sh
  docker container exec -it mynginx /bin/sh
  ```

- Use `ls` command to explore filesystem inside the container.

- Type `exit` and press `enter` to exit. This will just exit from the Shell process and won't stop the main process of the container, NGINX, in this case.

___

### - Inspect container logs

- Get all logs from `mynginx` container.

  ```sh
  docker container logs mynginx
  ```

- Keep following logs from `mynginx` container.

  ```sh
  docker container logs -f mynginx
  ```

- Press `Ctrl + C` on keyboard to exit and return back to terminal.

___

### - Start, Stop, and Delete an existing container

- First, get list of all containers.

  ```sh
  docker container ls -a
  ```

- Start a stopped container. Any container has been stopped can be started again. For example, using follwing command to start the `ubuntu-date` container you ran earlier.

  ```sh
  docker container start ubuntu-date
  ```

- Stop a running container. Use following command to stop `myginx` container you ran earlier.

  ```sh
  docker container stop mynginx
  ```

- Stop multiple running containers. Use following command to stop `ubuntu-date` and `nginx-daemon` containers you ran earlier.

  ```sh
  docker container stop ubuntu-date nginx-daemon
  ```

- Verify that there isn't any container running.

  ```sh
  docker container ls
  ```

- Delete a container. Use following command to delete `mynginx` container.

  ```sh
  docker container rm mynginx
  ```

- Verify that all containers are stopped e.g. the status is `Exited`.

  ```sh
  docker container ls -a
  ```

- Delete all stopped containers using this command. Enter 'y' to confirm deletion.

  ```sh
  docker container prune
  ```

- Verify that all containers have been removed.

  ```sh
  docker container ls -a
  ```

___

</details>

## Container Storage

<details>

<summary>Lab details</summary>

___

### - Docker Volume: named-volume

- Run a container with a named-volume by adding `-v` flag followed by volume name and path inside the container.

  ```sh
  docker container run -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb -v pgdata:/var/lib/postgresql/data --name postgresdb2 -d postgres
  ```

- Inspect the container to get volume information using the command below. Then look for `"Mounts"` section. You will see mount type, volume name, and its location.

  ```sh
  docker container inspect postgresdb2
  ```

- Verify that the `pgdata` volume gets created.

  ```sh
  docker volume ls
  ```

- (Optional) Use `ls` command to explore data inside the volume.

___

### - Docker Bind mount

- Run a container and mount `mypgdata` directory to the container with bind mount.

  ```sh
  docker run -d -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb -v $HOME/mypgdata:/var/lib/postgresql/data --name postgresdb3 postgres
  ```

- Inspect the container to get volume information using the command below. Then look for `"Mounts"` section. You will see mount type, volume name, and its location.

  ```sh
  docker container inspect postgresdb3
  ```

- Verify that `mypgdata` volume didn't get created.

  ```sh
  docker volume ls
  ```

- Use `ls` command to explore data inside the `$HOME/mypgdata` directory.

  ```sh
  ls $HOME/mypgdata
  ```

___

</details>

## Container Network

<details>

<summary>Lab details</summary>

___

### - Forward traffic from host's network to containers in `bridge` network

- Run a container with `bridge` network mode and add port forwarding by adding `-p 8080:80` flag so port `8080` on the host is mapped to port `80` of the container.

  ```sh
  docker container run -d --network bridge -p 8080:80 --name nginx2 nginx
  ```

- Use `curl` command to verify that the container running in the private `bridge` network with port forwarding now can be accessed from the host's network.

  ```sh
  curl http://localhost:8080
  ```

- Inspect the container to get IP address using the command below. Then look for `IPAddress` in the `"Networks"` section.

  ```sh
  docker container inspect nginx2
  ```

- Run a new container in the same `bridge` network and execute `wget` command to verify that the containers within the same `bridge` network can access to each other.

  ```sh
  docker container run --network bridge busybox wget -S -O- http://<nginx2 container IP address>
  ```

___

</details>

## Container Image

<details>

<summary>Lab details</summary>

___

### - Building container image

- Clone a project from GitHub.

  ```sh
  git clone https://github.com/audomsak/node-express-website
  ```

- Enter to the `node-express-website` directory.

  ```sh
  cd node-express-website
  ```

- View `Dockerfile` details.

  ```sh
  cat Dockerfile
  ```

- Build a new container image and specify repository and tag by adding `:` after the repository name and followed by a tag name.

  ```sh
  docker image build -t rhworkshop/node-website:1.0 .
  ```

- Get list of images to verify that a new container image is built with the specified repository and tag.

  ```sh
  docker image ls
  ```

- Run a container with the image you have just built.

  ```sh
  docker container run -d -p 1234:80 --name mywebsite rhworkshop/node-website:1.0
  ```

- Use `curl` command to verify that you can access to the website running in the container.

  ```sh
  curl http://localhost:1234
  ```

___

### - Tagging and versioning

- Add a new tag for the existing container image to have multiple tags or versions.

  ```sh
  docker image tag rhworkshop/node-website:1.0 rhworkshop/node-website:release
  ```

- Get list of container images to verify the new tag gets created.

  ```sh
  docker image ls
  ```

___

### - Pushing container image to Docker Hub

- Login to Docker hub with your Docker Hub username and password.

  ```sh
  docker login
  ```

- Push the container image to Docker Hub

  ```sh
  docker image push rhworkshop/node-website:1.0
  docker image push rhworkshop/node-website:latest
  ```

- Go to [Docker Hub website](https://hub.docker.com/) to verify the container image and tags.

___

</details>
