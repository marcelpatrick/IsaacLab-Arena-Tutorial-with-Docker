# IsaacLab-Arena-Tutorial-with-Docker

# Official Documentation 
1. https://developer.nvidia.com/blog/simplify-generalist-robot-policy-evaluation-in-simulation-with-nvidia-isaac-lab-arena
2. https://isaac-sim.github.io/IsaacLab-Arena/main/index.html
3. https://isaac-sim.github.io/IsaacLab-Arena/main/pages/quickstart/installation.html
4. https://github.com/isaac-sim/IsaacLab-Arena

# Pre-Requisites
- Have Ubuntu 22.04 installed. check ``wsl --list --verbose``
- Have Docker Desktop installed and running (open it and let it running on the background): check ``docker info | findstr "Operating System"``. You should see something like: Operating System: Docker Desktop
- Ubuntu has GPU access: test open Ubuntu and run ``docker run --rm --gpus all nvidia/cuda:12.2.0-base-ubuntu22.04 nvidia-smi``. You should see your GPU info if everything is configured correctly.

# Clone Arena Repo
-> Open Docker Desktop and let it run on the background
-> Open an Ubuntu terminal and inside it, do:

- Run: ``git clone https://github.com/isaac-sim/IsaacLab-Arena.git``

- navigate to folder: ``cd IsaacLab-Arena``
  
- initialize submodules
```
git config submodule.submodules/IsaacLab.url https://github.com/isaac-sim/IsaacLab.git
git config submodule.submodules/Isaac-GR00T.url https://github.com/NVIDIA/Isaac-GR00T.git

git submodule update --init --recursive
```

- Install isaaclab_arena in editable mode
```
cd IsaacLab-Arena
pip install -e .  
```

- Install Isaac Lab and all its modules
```
cd submodules/IsaacLab
./isaaclab.sh --install
```

- check ``ls -la docker/``. you should see the file ``run_docker.sh``

# Install xhost
Open your Ubuntu terminal (NOT inside a Docker container — your prompt should show (base)...
```
sudo apt-get update
sudo apt-get install -y x11-xserver-utils
```

# Set your Display Variable
We need to tell the container where to send the graphics via WSLg. Still on the (base) environment type: ``export DISPLAY=:0``


# install the Nvidia Docker container 
Make sure docker desktop is open on the background
Navigate to the root of the Nvidia Arena repository and install the docker container.
```
cd ~/IsaacLab-Arena
./docker/run_docker.sh
```

This will open the docker config .yaml file that will fetch the right docker image to install a docker container with all necessary dependencies to run Arena

```
  When you run ./docker/run_docker.sh, it:
  
  Reads docker-compose.yaml — This file specifies:
  
  Which NVIDIA base image to use (Isaac Sim 5.1.0)
  What to install on top (Isaac Lab, Arena)
  Which folders to share between Windows and the container
  GPU access settings
  
  Downloads the base image (~15-20 GB, first time only)
  
  Image: nvcr.io/nvidia/isaac-sim:5.1.0
  This is NVIDIA's official Isaac Sim container from NGC (NVIDIA GPU Cloud)
  
  Builds the Arena layer (~5 GB)
  
  Installs Isaac Lab
  Installs Arena
  Configures everything
  
  Starts the container and gives you a terminal inside it
```

# Fix Errors

The official docker file from Nvidia installs packages using the `glob` pattern `isaaclab*/` to find the packages to be installed, meaning it searches for "isaaclab" AND whatever comes after this. However, the core `isaaclab` package doesn't have anything after its name and thus never gets installed. And because isaaclab already contains all dependencies and subpackages necessary, none of these dependencies get installed either. Then, every other package that depends on it fails to import.

This fix adds an explicit pip install of isaaclab by its exact path, right after isaaclab.sh -i. Pip installs the package and resolves all its dependencies during the Docker build.

- On your Ubuntu host (not inside a container):
```
cd ~/IsaacLab-Arena

# Open file
nano docker/Dockerfile.isaaclab_arena
```
  - Find this line ``RUN ${ISAACLAB_PATH}/isaaclab.sh -i``
  - Add this line directly after it:
```
RUN /isaac-sim/python.sh -m pip install --no-build-isolation flatdict==4.0.1
RUN /isaac-sim/python.sh -m pip install -e ${WORKDIR}/submodules/IsaacLab/source/isaaclab
```
  - Save file with Ctrl O, Exit with Ctrl X

- Go back to Ubuntu terminal, outside of the container (type `exit`), and run: ``docker build --no-cache -t isaaclab_arena:latest -f docker/Dockerfile.isaaclab_arena .``

# Start Container and check dependencies

- Start a fresh container: ``docker run -it --gpus all --entrypoint bash isaaclab_arena:latest``
- Navigate to the isaaclab arena folder: ``cd /workspaces/isaaclab_arena``

- Verify that all dependencies are installed
Input these tests (with the double quotes in the end of each)
```
/isaac-sim/python.sh -c "import isaaclab; print('OK')"
/isaac-sim/python.sh -c "import warp; print('OK')"
/isaac-sim/python.sh -c "import pinocchio; print('OK')"
/isaac-sim/python.sh -c "import torch; print(torch.__version__)"
```

# Run Test
```
cd /workspace
pytest -sv isaaclab_arena/tests/

pytest -sv -m with_cameras isaaclab_arena/tests/
pytest -sv -m "not with_cameras" isaaclab_arena/tests/
```


# Run first example
```
python isaaclab_arena/examples/compile_env_notebook.py
```




  
