# Dev Container NVIDIA based
That is my VS Code DevContainer configuration with NVIDIA GPU Support and common libraries, such as `matplotlib` and `scikit`, installed.
As PyTorch and Jax are not installed by default, you can install mannually.

This work is heavily inspired by [the repo](https://github.com/alankrantas/cuda-cudnn-gpu-devcontainer) of Alan Wang.

You can also refer to [my blog post](https://lyk-love.cn/2024/04/26/vs-code-dev-container/) for some explanation.


## Prerequisites
- Docker engine (and setup .wslconfig to use more cores and memory than default)
- NVIDIA driver for the graphic card
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) (which is already included in Windows’ Docker Desktop; Linux users have to install it)
- VS Code with DevContainer extension installed

## Start the DevContainer

```sh
cd <your porject>
git clone git@github.com:LYK-love/VS-Code-DevContainer-Config.github
mv VS-Code-DevContainer-Config/.devcontainer ./
rm -r VS-Code-DevContainer-Config # delete this repo 
```
After that, in VS Code:
1. press `Ctrl + Shift + P` to bring up the Command Palette. 
2. Enter and find `Dev Containers: Reopen in Container`. 
3. VS Code will start downloading the docker image, run the script and install everything, and finish opening the directory in DevContainer.
4. The DevContainer would then run `nvidia-smi` to show what GPU can be seen by the container.

You can do more sanity checks like:
```sh
# Check nvcc and nvidia-smi
nvcc -V
nvidia-smi
```
# Details
The `./.devcontainer/devcontainer.json` is the config file, it leverages the docker image `lyklove/ml:1.0`, which is built from `./.devcontainer/Dockerfile`.

The system configuration of the image is showed in the `Dockerfile`, the base image used is `nvidia/cuda:12.4.1-cudnn-devel-ubuntu22.04`, which means:
* OS: Ubuntu22.04
* ISA: x86_64
* CUDA version: 12.4.1
* cuDNN is also installed in the image


# Custumization
If you want to change the content of the image,
1. Modify the `Dockerfile`.
2. Rebuild the image. You need to comment this line 
  ```json
  "image": "lyklove/ml:1.0", 
  ```
  and replace it with:
  ```json
  // "image": "lyklove/ml:1.0", 
  "build": { "dockerfile": "Dockerfile" },
  ```

Build and push the image (take my image as an example, so the image name is `ml` and tag is `1.0`):
```sh
docker build -t ml:1.0 ./.devcontainer
docker login
docker image tag ml:1.0 lyklove/ml:1.0
docker image push lyklove/ml:1.0 
```