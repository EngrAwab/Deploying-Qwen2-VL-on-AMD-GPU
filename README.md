# Qwen2-VL Deployment on AMD GPU with ROCm

This guide explains how to deploy the [Qwen2-VL-2B-Instruct](https://huggingface.co/Qwen/Qwen2-VL-2B-Instruct) model on an AMD GPU using ROCm + Docker. This deployment enables an API server (e.g. Flask) that accepts an image and returns a generated text description.

---

## ✅ 1. Verify ROCm Installation

Make sure your ROCm stack is correctly installed and detects your AMD GPU.

```bash  
rocm-smi  
```

If you see an error or the GPU is not listed, follow the official ROCm setup guide:  
👉 [ROCm Quick Start Guide](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/quick-start.html)

---

## ✅ 2. Set Up Docker

### a. Install Docker  
Follow the [Docker installation guide](https://docs.docker.com/get-docker/) for your OS.

### b. Add Docker permissions

```bash  
sudo usermod -aG docker $USER  
newgrp docker  
```

### c. Test Docker

```bash  
docker run hello-world  
```

---

## ✅ 3. Pull the ROCm PyTorch Docker Image

```bash  
docker pull rocm/pytorch:rocm6.2.3_ubuntu22.04_py3.10_pytorch_release_2.3.0  
```

---

## ✅ 4. Launch the Docker Container

```bash  
docker run -it --rm \
  --network=host \
  --device=/dev/kfd \
  --device=/dev/dri \
  --group-add=video \
  --ipc=host \
  --cap-add=SYS_PTRACE \
  --security-opt seccomp=unconfined \
  --shm-size 8G \
  --hostname=ROCm-FT \
  -v $(pwd):/workspace \
  -w /workspace \
  rocm/pytorch:rocm6.2.3_ubuntu22.04_py3.10_pytorch_release_2.3.0  
```

---

## ✅ 5. Install Python Dependencies

Inside the container:

```bash  
pip install torch torchvision  
pip install transformers accelerate peft scipy tensorboardX  
pip install git+https://github.com/huggingface/transformers  
pip install git+https://github.com/huggingface/peft  
pip install git+https://github.com/huggingface/accelerate  
pip install sentencepiece timm  
```

---

## ✅ 6. (Optional) Install ROCm-Compatible BitsAndBytes

```bash  
git clone --recurse https://github.com/ROCm/bitsandbytes  
cd bitsandbytes  
git checkout rocm_enabled_multi_backend  
pip install -r requirements-dev.txt  
cmake -DCOMPUTE_BACKEND=hip -S .  
make  
pip install .  
```

---
## ✅ 7. Clone This Repository and Run the Notebook

```bash  
git clone https://github.com/EngrAwab/Deploying-Qwen2-VL-on-AMD-GPU.git
cd Deploying-Qwen2-VL-on-AMD-GPU   
pip install jupyter  
```

Start the Jupyter Lab server:

```bash  
jupyter-lab --ip=0.0.0.0 --port=8888 --no-browser --allow-root  
```

> 📌 **Note**: Ensure port `8888` is not already in use. If needed, change it (e.g. `--port=8890`).

Then open the Jupyter notebook in your browser and **run all the cells** to start generating image descriptions using Qwen2-VL.

---

