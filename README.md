# SignalFlow-Preprocessing
## SignalFlow Docker Stacks

*SignalFlow-Preprocessing* is a ready-to-run workstation in your browser for Matlab-based EEG preprocessing pipelines. 

The Docker container contains key software: Matlab 2023a and open-source toolkits (i.e., EEGLAB). The container uses your own data directories and MATLAB license. Changes to the linked data directories stay persistent even after stopping the container. The container can be deployed on any number of workstations and the image is standardized to be fully functional.

![Demonstration](sfpre.gif)

The current images are built using advanced VNC software (Kasm workstation) and should be comparable to working on a local desktop PC. We have made two containers available based on Ubuntu 20.04 LTS - a CPU-only container and a larger CUDA GPU-enabled container. 

## Quick Start
Example:
* :cpu (CPU only)
``` 
docker run --rm -it --shm-size=512m -p 10100:6901 -v /path/to/local/storage/:/srv \
       -v path/to/license.lic:/licenses/license.lic cincibrainlab/signalflow-preprocessing:cpu
```

* :gpu (Nvidia CUDA GPU support)
```
docker run --rm -it --shm-size=512m -p 10100:6901 -v /path/to/local/storage/:/srv --gpus:all \ 
       cincibrainlab/signalflow-preprocessing:gpu
```

Access MATLAB via browser at https://localhost:10100 with the user `kasm_user` and password `vncpassword`

- port can be modified to run multple simutaneous instances on same PC
- use 'https' NOT 'http'
- change default password by specifying environmental variable `VNC_PW` (i.e., -e VNC_PW=otherpassword)
- see licensing below for troubleshooting
- access PC through network by specifying IP address / hostname + any firewall protection

DockerHub: https://hub.docker.com/repository/docker/cincibrainlab/signalflow-preprocessing/general


## MATLAB Licensing Options

Choose one of the methods below to associate a license with your container:

### 1. Web Login:
- Launch MATLAB 2023a without a valid license file to get a login prompt for web activation.

### 2. License Server:
- Modify the `MLM_LICENSE_FILE` environmental variable to point to a valid MATLAB license server.
- Ensure a valid hostname is provided so the server acknowledges it.

### 3. Direct License File Linking:
- MATLAB in the container defaults to checking `/licenses/license` for a license file.
- To connect a license, use the `volume` command to map your license file to the above path.

#### Network, Individual, or Student License:

**Docker Run**:
```
# Student Example
docker run --rm -it --shm-size=512m -e STUDENT_USER=<USERNAME> --hostname <HOSTID> --mac-address <00:00:00:00:00:00> -v C:\dev\licenses\license.lic:/licenses/license.lic -p 10100:6901 cincibrainlab/signalflow-preprocessing:cpu

# Network License Example
docker run --rm -it --shm-size=512m --hostname ClusterAccount -v C:\dev\licenses\network.lic:/licenses/license.lic -p 10100:6901 cincibrainlab/signalflow-preprocessing:cpu

```

**Docker Compose**:
```
volumes:
    - ${MATLAB_LICENSE_FILE}:/licenses/license.lic  # File name specified in .env file
```

Ensure `HOSTNAME` and `MAC_ADDRESS` align with the license's requirements.

---

URLs:
- [MATLAB Licensing Documentation](https://www.mathworks.com/services/licensing.html) (For reference; added for completeness)

## Features
Provides a full Linux desktop environment with XFCE, a scientific computing environment with Matlab preconfigured for EEG preprocessing.

*Matlab Toolboxes installed suitable for Signalflow, EEGLAB, Fieldtrip, HAPPE, MADE pipelines*
- Parallel Computing Toolbox
- Image Processing Toolbox  
- Signal Processing Toolbox
- Image Processing Toolbox
- Optimization Toolbox
- Statistics and Machine Learning Toolbox
- Wavelet Toolbox
- Curve Fitting Toolbox

* local use is added to the SUDO group. If container is modified, you can persist changes by using `docker commit` and using the saved image on subsequent `docker run` or `docker-compose` commands.

## MATLAB Student Licenses

To change the username that executes MATLAB configure an environmental variable `STUDENT_USER`. This user will adopt the environment and permissions of the `kasm_user` but MATLAB will see the `STUDENT_USER` that will match the license file. `HOSTNAME` and `MAC_ADDRESS` can also be set. A convenience MATLAB icon, named MATLAB 2023a (Student) is placed on the default desktop.

## Repository Files

* build-sfpreprocessing_*.sh: build command to create local container images for cpu and gpu versions
* Dockerfile-signalflow-preprocessing-cpu: Source for CPU image
* Dockerfile-signalflow-preprocessing-gpu: Source for GPU image
* install_eeg_toolkits.sh: shell script for installing EEG toolkits
* env.template: environmental variable template (rename to .env for production)
* src/: KASM provided additional software setup scripts

# Build instructions

1. Clone repository by selecting the signalflow-preprocessing branch

```bash
git clone -b signalflow-preprocessing https://github.com/cincibrainlab/signalflow-stacks.git
```

2. enter the build directory

```bash
cd build
```

3. Modify either the Docker-signalflow-preprocessing-* Dockerfiles directory or modify the post-processing installs cript install_eeg_toolkits.sh. 

4. Run a docker build command (can modify tags)

```bash
# build signalflow-preprocessing:cpu 
docker build -t cincibrainlab/signalflow-preprocessing:cpu -f Dockerfile-signalflow-preprocessing-cpu .

# build signalflow-preprocessing:cpu 
docker build -t cincibrainlab/signalflow-preprocessing:gpu-f Dockerfile-signalflow-preprocessing-gpu .
```

5. Check image by either using `docker-compose` or `docker run` command (see above)


## License

This project is licensed under the terms of the MIT License.

## Maintainer
This project is maintained by Ernest Pedapati (ernest.pedapati@cchmc.org).
