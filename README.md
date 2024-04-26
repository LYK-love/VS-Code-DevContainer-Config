# Dev Container NVIDIA based
That is my VS Code DevContainer configuration with NVIDIA GPU Support and common libraries, such as PyTorch and Jax, installed.

This work is heavily inspired by [the repo](https://github.com/alankrantas/cuda-cudnn-gpu-devcontainer) of Alan Wang.

You can also refer to my [blog post]() for some explanation.


## Prerequisites
- Docker engine (and setup .wslconfig to use more cores and memory than default)
- NVIDIA driver for the graphic card
- NVIDIA Container Toolkit (which is already included in Windows’ Docker Desktop; Linux users have to install it)
- VS Code with DevContainer extension installed

## Start the DevContainer
- Clone this repo.
- In VS Code press `Ctrl + Shift + P` to bring up the Command Palette. 
- Enter and find `Dev Containers: Reopen in Container`. 
- VS Code will starts to download the CUDA image, run the script and install everything, and finish opening the directory in DevContainer.
- The DevContainer would then run `nvidia-smi` to show what GPU can be seen by the container. Be noted that this works even without setting up cuDNN or any environment variables.

You can do more sanity checks like:
```sh
# Check if cuDNN is installed correctly. This can be done by checking the jax code:
python -c "import jax; m = jax.numpy.array([1,]); m@m"

# Check nvcc and nvidia-smi
nvcc -V
nvidia-smi
```
# Details
The `./.devcontainer/devcontainer.json` is the config file, it leverages the docker image `s`, whose `Dockerfile` is also provided in `./.devcontainer`.

The system configuration of the image is in the `Dockerfile`, the base image  used is `nvidia/cuda:12.4.1-cudnn-devel-ubuntu22.04`, which means:
* OS: Ubuntu22.04
* ISA: x86_64
* CUDA version: 12.4.1
* cuDNN is also installed in the image


# Custumization
If you want to change the content of the image,
1. Modify the `Dockerfile`.
2. Rebuild the image. You need to comment the 
  ```json
  ```
  and replace it with:
  ```json
  "build": { "dockerfile": "Dockerfile" },
  ```

