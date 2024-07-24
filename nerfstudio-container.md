# Use docker image
Instead of installing and compiling prerequisites, setting up the environment and installing dependencies, a ready to use docker image is provided.

Source: https://docs.nerf.studio/quickstart/installation.html


## Prerequisites
Docker (get docker) and nvidia GPU drivers (get nvidia drivers), capable of working with CUDA 11.8, must be installed. The docker image can then either be pulled from here (replace with the actual version, e.g. 0.1.18)

```console
docker pull dromni/nerfstudio:<version>
```

or be built from the repository using

```console
docker build --tag nerfstudio -f Dockerfile .
```

To restrict to only CUDA architectures that you have available locally, use the CUDA_ARCHITECTURES build arg and look up the compute capability for your GPU. For example, here’s how to build with support for GeForce 30xx series GPUs:

```console
docker build \
    --build-arg CUDA_VERSION=11.8.0 \
    --build-arg CUDA_ARCHITECTURES=86 \
    --build-arg OS_VERSION=22.04 \
    --tag nerfstudio-86 \
    --file Dockerfile .
```

The user inside the container is called ‘user’ and is mapped to the local user with ID 1000 (usually the first non-root user on Linux systems).

If you suspect that your user might have a different id, override USER_ID during the build as follows:

```console
docker build \
    --build-arg USER_ID=$(id -u) \
    --file Dockerfile .
```

## Using an interactive container
The docker container can be launched with an interactive terminal where nerfstudio commands can be entered as usual. Some parameters are required and some are strongly recommended for usage as following:

```console
docker run --gpus all \                                         # Give the container access to nvidia GPU (required).
            -u $(id -u) \                                       # To prevent abusing of root privilege, please use custom user privilege to start.
            -v /folder/of/your/data:/workspace/ \               # Mount a folder from the local machine into the container to be able to process them (required).
            -v /home/<YOUR_USER>/.cache/:/home/user/.cache/ \   # Mount cache folder to avoid re-downloading of models everytime (recommended).
            -p 7007:7007 \                                      # Map port from local machine to docker container (required to access the web interface/UI).
            --rm \                                              # Remove container after it is closed (recommended).
            -it \                                               # Start container in interactive mode.
            --shm-size=12gb \                                   # Increase memory assigned to container to avoid memory limitations, default is 64 MB (recommended).
            dromni/nerfstudio:<tag>                             # Docker image name if you pulled from docker hub.
            <--- OR --->
            nerfstudio                                          # Docker image tag if you built the image from the Dockerfile by yourself using the command from above.
```

## Call nerfstudio commands directly
Besides, the container can also directly be used by adding the nerfstudio command to the end.

```console
docker run --gpus all -u $(id -u) -v /folder/of/your/data:/workspace/ -v /home/<YOUR_USER>/.cache/:/home/user/.cache/ -p 7007:7007 --rm -it --shm-size=12gb  # Parameters.
            dromni/nerfstudio:<tag> \                           # Docker image name
            ns-process-data video --data /workspace/video.mp4   # Smaple command of nerfstudio.
```

