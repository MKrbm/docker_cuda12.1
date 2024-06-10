## Dockerfile
- Ubuntu22.04
- CUDA12-1
- CPP
- Miniconda
- neovim

## .devcontainer
- Can be used together with vscode / cursor



## Prerequisites  

###  Docker
###  Nvidia Driver
###  Cuda Toolkit
- Run `./cuda-toolkit-setup.sh` to install cuda toolkit
- To configure the runtime for docker, run the following command:
  ```
  sudo nvidia-ctk runtime configure --runtime=docker
  ```
  and restart docker service with
  ```
  systemctl restart docker
  ```


Check the following links for more info:
- https://github.com/NVIDIA/nvidia-docker/tree/master#quickstart


