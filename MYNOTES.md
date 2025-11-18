# My Notes

## Building from source ##

Build the image locally using this git repository as the [build context](https://docs.docker.com/engine/reference/commandline/build/#git-repositories):

```console
docker build \
  --tag felddy/foundryvtt:13.351.0 \
  https://github.com/felddy/foundryvtt-docker.git#develop
```

## Cross-platform builds ##

To create images that are compatible with other platforms you can use the
[`buildx`](https://docs.docker.com/buildx/working-with-buildx/) feature of
Docker:

1. Copy the project to your machine using the `Clone` button above
   or the command line:

    ```console
    git clone https://github.com/felddy/foundryvtt-docker.git
    cd foundryvtt-docker
    ```

1. Build the image using `buildx`:

    ```console
    docker buildx build \
      --platform linux/amd64 \
      --output type=docker \
      --tag felddy/foundryvtt:13.351.0 .
    ```

## Pre-installed distribution builds ##

It is possible to install a Foundry Virtual Tabletop distribution into the
Docker image at build-time.  This results in a significantly larger Docker
image, but removes the need to install a distribution at container startup,
resulting in a faster startup.  It also moves the user authentication to
build-time instead of start-time.

### Image build with credentials ###

> [!NOTE]
> Credentials are only used to fetch a distribution, and are not stored
> in the resulting image.

```console
docker build \
  --secret id=foundry_username,src=<(echo "<your_username>") \
  --secret id=foundry_password,src=<(echo "<your_password>") \
  --tag felddy/foundryvtt:13.351.0 \
  https://github.com/felddy/foundryvtt-docker.git#develop
```

### Image build with a temporary URL ###

```console
docker build \
  --build-arg FOUNDRY_RELEASE_URL='<temporary_url>' \
  --tag felddy/foundryvtt:13.351.0 \
  https://github.com/felddy/foundryvtt-docker.git#develop
```

# Running

```console
docker run \
  --env FOUNDRY_RELEASE_URL='<temporary_url>' \
  --publish 30000:30000/tcp \
  --volume <your_data_dir>:/data \
  felddy/foundryvtt:13
```

# My Build

Build including foundry software
```console
docker buildx build \
  --output type=docker \
  --build-arg FOUNDRY_RELEASE_URL='https://r2.foundryvtt.com/releases/13.351/FoundryVTT-Node-13.351.zip?verify=1763175665-5RENGQ54nktxQ0rytPEvt7TBvYY0dkObBgzuHbjtAt4%3D' \
  --tag altbier/waterdeep-foundryvtt:13.351.1 .
```

Build without foundry software, when using CONTAINER_CACHE at runtime
```console
docker buildx build \
  --output type=docker \
  --tag altbier/waterdeep-foundryvtt:13.351.2 .
```

  --build-arg CONTAINER_PRESERVE_CONFIG=true \ <- doesnt work, var only works at run time
  --build-arg FOUNDRY_HOSTNAME='waterdeep.altbier.us' \
  --build-arg CONTAINER_CACHE='/data/container_cache' \ <- doesnt work, var only works at run time


# My Run

```console
docker run \
  --publish 30000:30000/tcp \
  --mount type=bind,source=/c/Users/richa//OneDrive/games/waterdeep_adventures/altbier_vtt_data,target=/data \
  --env FOUNDRY_HOSTNAME='waterdeep.altbier.us' \
  altbier/waterdeep-foundryvtt:13.351.1
```

```console
docker run \
  --publish 30000:30000/tcp \
  --volume waterdeep_vtt_data:/data \
  altbier/waterdeep-foundryvtt:13.351.0
```

## Compose

To start it:
```console
docker compose up -d
```

To Stop it:
```console
docker compose down
```

See the compose.yml file for details on the config

Some compose notes:

https://docs.docker.com/reference/cli/docker/compose/

docker compose up: Creates and starts all services defined in the Compose file.
docker compose down: Stops and removes containers, networks, and optionally volumes.
docker compose build: Builds or rebuilds service images.
docker compose pull: Downloads service images from a registry.
docker compose start/stop: Starts or stops specific services.
docker compose ps: Displays the status of running services.
docker compose logs: Streams the log output of running services.
docker compose exec: Executes a command in a running container.
