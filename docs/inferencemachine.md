# AI Inference Machine

## Plan

Build an inference server from used parts.

1. Start with a workstation PC capable of having at least 2 PCIe x16 cards AND an additional video out (whether on board or 3rd GPU).
2. Get 2 or more server-grade GPUs.
3. Fit additional GPU cooling if necessary.
4. Add networking, RAM and storage if not included.
5. Install RHEL AI Inference Server (or other option) and go to town.

## Shopping List

- [x] Workstation (**Dell Precision 5820** or ~~HP Z4G4~~)
- [x] 2 Server-grade gpus (AMD Radeon Pro v620)
- [ ] Fan/blower
- [ ] Shroud
- [x] ~~RAM~~ (included with workstation)
- [x] ~~Disk~~ (included with workstation)
- [ ] Cabling


``` bash
podman run -d \
  --name vllm \
  --device /dev/kfd \
  --device /dev/dri \
  -p 8000:8000 \
  -v $HOME/.cache/huggingface:/root/.cache/huggingface:Z \
  --ipc=host \
  vllm/vllm-openai:rocm \
  --model ibm-granite/granite-3.0-8b-instruct
```

``` bash
podman run -d \
  -p 3000:8080 \
  -e OLLAMA_BASE_URL=http://<workstation-ip>:11434 \
  -v open-webui:/app/backend/data:Z \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
  ```


``` bash
# Ensure subscription-manager is registered, then update
sudo dnf update -y

# Enable codeready and EPEL repos
sudo subscription-manager repos --enable codeready-builder-for-rhel-9-$(arch)-rpms
sudo dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

# Add the official AMD ROCm repository for RHEL 10
sudo dnf install -y https://repo.radeon.com/amdgpu-install/7.2.4/rhel/9.7/amdgpu-install-7.2.4.70204-1.el9.noarch.rpm

# Clean cache and install the amgdpu-dkms kernel modules, kernel headers, and development tools
sudo dnf clean all
sudo dnf install -y kernel-devel kernel-headers kernel-devel-matched dkms gcc gcc-c++ make epel-release

# Reboot to ensure you are running the exact kernel version matching your headers
sudo reboot

# Add your user account to the hardware rendering groups
sudo usermod -aG video,render $USER

# Persistently allow Podman containers to utilize host acceleration devices
sudo setsebool -P container_use_devices 1

# Install Podman if it wasn't selected during RHEL base install
sudo dnf install -y podman

# Create a local directory for caching Hugging Face model weights
mkdir -p $HOME/.cache/huggingface

# Run the container
podman run -d \
  --name vllm-server \
  --device /dev/kfd \
  --device /dev/dri \
  -p 8000:8000 \
  -v $HOME/.cache/huggingface:/root/.cache/huggingface:Z \
  --ipc=host \
  --security-opt label=type:container_runtime_t \
  vllm/vllm-openai-rocm:latest \
  --model ibm-granite/granite-3.0-8b-instruct \
  --host 0.0.0.0 \
  --port 8000

# Stop and remove the temporary single-GPU instance
podman stop vllm-server && podman rm vllm-server

# Relaunch leveraging BOTH GPUs natively across the model split
podman run -d \
  --name vllm-server \
  --device /dev/kfd \
  --device /dev/dri \
  -p 8000:8000 \
  -v $HOME/.cache/huggingface:/root/.cache/huggingface:Z \
  --ipc=host \
  --security-opt label=type:container_runtime_t \
  vllm/vllm-openai:rocm \
  --model ibm-granite/granite-3.0-8b-instruct \
  --tensor-parallel-size 2 \
  --host 0.0.0.0 \
  --port 8000

sudo firewall-cmd --add-port=8000/tcp
sudo firewall-cmd --add-port=8000/tcp --permanent



```






Notes:
- [https://github.com/xCuri0/ReBarUEFI/discussions/331#discussioncomment-17285915]
- 


[↩️ Back to the README](../README.md)
