# Docker Hands-on Lab for a beginner

## Before start

Some of follwoing lab instructions contain both [solo and management commands](https://blog.couchbase.com/docker-1-13-management-commands/). Both types of commands do the same things so you can choose one over the other as you like. The intention of having both command types in the lab is to let you be aware of and familiarize with them.

___

## Container Usages

<details>

<summary>Lab details</summary>

___

### - Pull container image form Docker Hub

By default, Docker will be pulling container images from Docker Hub if you didn't specify any registry or repository.

- Pulling a NGINX container image *without* specifying a tag. Docker will pull the image with `latest` tag.

  ```sh
  docker pull nginx
  # OR
  docker image pull nginx
  ```

- Pull container image with a specific tag by adding `:` after the image name followed by a tag name.

  ```sh
  docker pull nginx:1.21
  # OR
  docker image pull nginx:1.21
  ```

___

### - Pull container image from other registries

Apart from Docker Hub, Docker can also pull container images from other private and public registries or repositories.

- Pulling an Open JDK 8 container image from [quay.io](quay.io).

  ```sh
  docker image pull quay.io/public/openjdk:8-slim
  ```

___

### - Get list of container images

- Get list of container images on the host.

  ```sh
  docker images
  # OR
  docker image ls
  ```

___

### - Run a container with container image in local

- Run a container with the image you just pulled above.

  ```sh
  docker run nginx:1.21
  # OR
  docker container run nginx:1.21
  ```

- Press `Ctrl + C` on keyboard to stop and exit from the container.

___

### - Run a container without container image in local

Any container can be run without pulling the container image manually. Docker will be finding the container image in local first, if the container image doesn't exist then it will try to pull the container image from container image repository (Docker Hub).

- Run a container without pulling container image.

  ```sh
  docker container run httpd
  ```

- Press `Ctrl + C` on keyboard to stop and exit from the container.

___

### - Run a container and give it a name

Normally, a container will be given a random name when you run it without giving it a name. However, random name is hard to remember and doesn't indicate what application running in the container is.

- Run a container and name it by adding `--name` flag followed by a name you need to assign to the container. The container name can be used as a reference with other commands i.e. `docker container stop`.

  ```sh
  docker run --name nginxserver nginx:1.21
  # OR
  docker container run --name nginxserver nginx:1.21
  ```

- Press `Ctrl + C` on keyboard to stop and exit from the container.

___

### - Run a container and override its default command

A default command (specified in Dockerfile) will get run when you run or start a container. However, you can also override the default command with new command(s) you need to execute inside the container.

Please note that the container must have binary or execution file(s) for any new command(s) you need to execute inside the container. For example, if you need to execute `wget` inside the container then the `wget` binary must exist inside the container.

- Run a container and override its default command with a single command

  ```sh
  docker run nginx:1.21 ls -la
  # OR
  docker container run nginx:1.21 ls -la
  ```

- Override with multiple commands

  ```sh
  docker run nginx:1.21 sh -c 'uname -a && pwd && ls -la'
  # OR
  docker container run nginx:1.21 sh -c 'uname -a && pwd && ls -la'
  ```

___

### - Set environment variable for a container

Each container has it own set of environment variables inside the container separately from the host. Some of containers might need environment variable(s) to run i.e. username, password, configs etc. or you might need to inject some values into the container which can be done via environment variables as well.

- Run a container and inject an environment variable using `-e` flag followed by environment variable name and its value.

  ```sh
  docker run -e MESSAGE="Hello world" ubuntu sh -c 'echo $MESSAGE'
  ```

  The command above injects a `MESSAGE` environment variable into the Ubuntu container so the `MESSAGE` environment variable can be used with `echo` command.

- Run a container and inject multiple environment variables.

  ```sh
  docker run -e MESSAGE1="Hello" -e MESSAGE2="world" ubuntu sh -c 'echo $MESSAGE1 $MESSAGE2'
  ```

___

### - Running a container in interactive mode

Usually, Docker will bind standard input (`stdin`), standard output (`stdout`), and standard error (`stderr`) (which usually is your terminal) to the container's `tty` when running a container. That's why you can see some outputs i.e. logs generated from the container.

However, some containers don't have the processes that keep running which then keep your terminal connected to the container's `tty`. Ubuntu container, for example, wich doesn't have any process that will keep running inside the container. It's default command that will be run when the container gets started is `bash` which does nothing but a Bash shell. So, if you need to keep your terminal connected to the container's `tty` then you can run the container with interactive mode.

- First, run a container without interactive mode. You won't see any output like you ran NGINX container previously because the default command of Ubuntu container is `bash` and it doesn't do anything so it just runs and stops immediately.

  ```sh
  docker run ubuntu
  # OR
  docker container run ubuntu
  ```

- Run a container again but this time with interactive mode by adding `-it` flag. Your terminal will get connected to the container's `tty` and you will get Bash shell prompt.

  ```sh
  docker run -it ubuntu
  # OR
  docker container run -it ubuntu
  ```

- Then run this command to check OS name, build number, architecture etc.

  ```sh
  uname -a
  ```

- Exit from the container by typing `exit` and press `Enter` on keyboard.

- Then run `uname -a` again and compare the command outputs. You will see the different of outputs from the container and the host itself.

___

### - Run a container in detatch mode

Usually, as explained above, your terminal will get connected or binded to the container's `tty` when running a container and obviously that you won't be able to run any commands on the host with that terminal because the terminal is already dedicated to the container. If you need to run a container but don't need to tie your terminal to it then you can run the container in detatch mode.

Also, sometime you might need to run the container as a background process like a daemon process. To do so, you can run a container with detatch mode as well.

- Run NGINX container in detatch mode by adding `-d` flag. The container will be running in background like daemon processes.

  ```sh
  docker run -d --name nginx-daemon nginx
  # OR
  docker container run -d --name nginx-daemon nginx
  ```

  Docker will print out only container ID and you won't see any output or logs.

- Use following command to check if the container is running.

  ```sh
  docker ps
  # OR
  docker container ls
  ```

___

### - Get list of containers

- Get list of `running` containers.

  ```sh
  docker ps
  # OR
  docker container ls
  ```

- Get list of all containers regardless of their statuses.

  ```sh
  docker ps -a
  # OR
  docker container ls -a
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
  docker attach ubuntu-date
  # OR
  docker container attach ubuntu-date
  ```

  You'll see date and time get printed out for every second.

- Press `Ctrl + C` on keyboard to stop and exit from the container.

___

### - Execute command inside a container from host

While any container running in background (`detatch mode`), you can run command(s) inside those container without jumping into the container i.e. attach to the container.

Please note that the container must have binary or execution file(s) for the command(s) you need to execute inside the container. For example, if you need to execute `wget` inside the container then the `wget` binary must exist in the container as well.

- First, run a container in detatch mode.

    ```sh
  docker run -d --name mynginx nginx:1.21
  # OR
  docker container run -d --name mynginx nginx:1.21
  ```

- Run `env` command inside `mynginx` container.

  ```sh
  docker exec mynginx env
  # OR
  docker container exec mynginx env
  ```

___

### - Inspect a container

You can inspect the details of any container to see its details i.e. Network, Mount, Configurations and so on.

- Inspect `mynginx` container you ran earlier to see its details.

  ```sh
  docker inspect mynginx
  # OR
  docker container inspect mynginx
  ```

___

### - Inspect inside a running container

You can inspect and see what's inside a running container by executing the Shell i.e. bash inside the container with interactive mode and use `ls` command to explore file system hierarchy inside the container.

- Get an interactive terminal for `mynginx` container.

  ```sh
  docker exec -it mynginx /bin/sh
  # OR
  docker container exec -it mynginx /bin/sh
  ```

- Use `ls` command to explore filesystem inside the container.

- Type `exit` and press `enter` to exit. This will just exit from the Shell process and won't stop the main process of the container, NGINX, in this case.

___

### - Inspect container logs

- Get all logs from `mynginx` container.

  ```sh
  docker logs mynginx
  # OR
  docker container logs mynginx
  ```

- Keep following logs from `mynginx` container.

  ```sh
  docker logs -f mynginx
  # OR
  docker container logs -f mynginx
  ```

- Press `Ctrl + C` on keyboard to exit and return back to terminal.

___

### - Manage containers

- First, get list of all containers.

  ```sh
  docker ps -a
  # OR
  docker container ls -a
  ```

- Start a stopped container. Any container has been stopped can be started again. For example, using follwing command to start the `ubuntu-date` container you ran earlier.

  ```sh
  docker start ubuntu-date
  # OR
  docker container start ubuntu-date
  ```

- Stop a running container. Use following command to stop `myginx` container you ran earlier.

  ```sh
  docker stop mynginx
  # OR
  docker container stop mynginx
  ```

- Stop multiple running containers. Use following command to stop `ubuntu-date` and `nginx-daemon` containers you ran earlier.

  ```sh
  docker stop ubuntu-date nginx-daemon
  # OR
  docker container stop ubuntu-date nginx-daemon
  ```

- Verify that there isn't any container running.

  ```sh
  docker ps
  # OR
  docker container ls
  ```

- Delete a container. Use following command to delete `mynginx` container.

  ```sh
  docker rm mynginx
  # OR
  docker container rm mynginx
  ```

- Verify that all containers are stopped e.g. the status is `Exited`.

  ```sh
  docker ps -a
  # OR
  docker container ls -a
  ```

- Delete all stopped containers using this command. Enter 'y' to confirm deletion.

  ```sh
  docker container prune
  ```

- Verify that all containers have been removed.

  ```sh
  docker ps -a
  # OR
  docker container ls -a
  ```

___

</details>

## Container Storage

<details>

<summary>Lab details</summary>

___

### - Docker Volume: named-volume

Docker volume can be categorized into two types; Anonymous volume and Named-volume. Anonymous volume's name is randomly generated, not human readible and hard to remember. It will be a nightmare if you run many containers that use many anonymous volumes because it will be hard to figure out which volume belongs to which container. Named-volume can help in this case.

- Run a container with a named-volume by adding `-v` flag followed by volume name and path inside the container separated by `:`.

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

Volume is managed by Docker and stored in a specific location on host e.g. `/var/lib/docker/volumes`. If you need to store data produced from a container anywhere on the host then you can use bind mount to mount any directory on the host to a directory inside the container.

You can also use bind mount to inject any directory into the container as well. For example, you can inject a directory of Java project into a Maven container then execute the maven commands inside the container to compile, build, run and so on.

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

### - Bridge network mode

 The `bridge` network is a default network and containers will be running in this network automatically when you run any container and didn't specify the `--network` flag. However, adding `--network` flag is more clear and helps you to quickly know in which network the container will be running.

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

- Build a new container image and tag it by adding `-t` flag followed by repository name and tag name seprated by `:`.

  ```sh
  docker image build -t <your Docker Hub account>/node-website:1.0 .
  ```

- Get list of images to verify that a new container image is built with the specified repository and tag.

  ```sh
  docker image ls
  ```

- Run a container with the image you have just built.

  ```sh
  docker container run -d -p 1234:80 --name mywebsite <your Docker Hub account>/node-website:1.0
  ```

- Use `curl` command to verify that you can access to the website running in the container.

  ```sh
  curl http://localhost:1234
  ```

___

### - Tagging and versioning

- Add a new tag for the existing container image to have multiple tags or versions.

  ```sh
  docker image tag <your Docker Hub account>/node-website:1.0 <your Docker Hub account>/node-website:release
  ```

- Get list of container images to verify the new tag gets created.

  ```sh
  docker image ls
  ```

___

### - Pushing container image to Docker Hub

- Login to Docker Hub with your username and password.

  ```sh
  docker login
  ```

- Push the container image to Docker Hub

  ```sh
  docker image push <your Docker Hub account>/node-website:1.0
  docker image push <your Docker Hub account>/node-website:release
  ```

- Go to [Docker Hub website](https://hub.docker.com/) to verify the container image and tags.

___

</details>
