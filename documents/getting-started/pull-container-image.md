# Pull Container Image From Container Image Registry

## Pull container image from Docker Hub

By default, Docker will be pulling container images from Docker Hub if you didn't specify any registry or repository.

- Pulling a **NGINX** container image ***without*** specifying a tag. Docker will pull the image with the `latest` tag (if exists) automatically.

  ```sh
  docker pull nginx
  ```

  OR

  ```sh
  docker image pull nginx
  ```

- Pull container image with a specific tag by adding `:` after the image name followed by a tag name.

  ```sh
  docker pull nginx:1.21
  ```

  OR

  ```sh
  docker image pull nginx:1.21
  ```

## Pull container image from other container image registries

Apart from Docker Hub, Docker can also pull container images from other private and public container image registries.

- Pulling a **NGINX** container image from [quay.io](quay.io).

  ```sh
  docker pull quay.io/aptible/nginx
  ```

  OR

  ```sh
  docker image pull quay.io/aptible/nginx
  ```
