# Deepseek-locally-using-Docker

Here's how to run DeepSeek locally using **Ollama** and **Docker** without relying on an official DeepSeek image. I've tried this steps by myself and optimized for clarity:

---

### **Step 1: Install Ollama and Docker** 
1. **Install Ollama**:
   ```bash
   curl -fsSL https://ollama.ai/install.sh | sh
   ```
   Verify with `ollama --version`.

2. **Install Docker**:
   - For Linux:
     ```bash
     curl -fsSL https://get.docker.com | sh
     sudo usermod -aG docker $USER  # Reboot afterward
     ```
   - For Windows/macOS: Download Docker Desktop from [docker.com](https://www.docker.com/products/docker-desktop/).

---

### **Step 2: Run Ollama in a Docker Container** 
Start the Ollama container with persistent storage and API access:
```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```
- `-v ollama:/root/.ollama`: Persists model data.
- `-p 11434:11434`: Exposes Ollama’s API port.

---

### **Step 3: Pull and Run DeepSeek Inside the Container** 
1. **Access the container’s terminal**:
   ```bash
   docker exec -it ollama /bin/bash
   ```

2. **Download your preferred DeepSeek model** (adjust parameter size as needed):
   ```bash
   ollama pull deepseek-r1:8b  # Example: 8B parameter model
   ```
   Supported models: `1.3b`, `7b`, `14b`, `32b`, `70b`, etc. (see hardware requirements below) .

3. **Run the model**:
   ```bash
   ollama run deepseek-r1:8b
   ```
   Test with prompts directly in the terminal.

---

### **Step 4: Optional Web Interface (OpenWebUI)** 
For a GUI experience, deploy OpenWebUI alongside Ollama:
```bash
docker run -d -p 3000:8080 -e OLLAMA_BASE_URL=http://localhost:11434 -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```
Access the interface at `http://localhost:3000` to interact with DeepSeek via a browser.

---

### **Hardware Requirements** 
| Model Size   | Minimum RAM | Recommended Use Case          |
|--------------|-------------|--------------------------------|
| **1.3B**     | 8GB         | Basic chatbots, lightweight tasks |
| **7B**       | 16GB        | Coding assistance, general AI tasks |
| **14B**      | 24GB        | Advanced reasoning, coding    |
| **32B+**     | 64GB+       | Enterprise/research workloads |

- **GPU Acceleration**: Add `--gpus all` to the Docker run command if you have an NVIDIA GPU (requires [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)) .

---

### **Troubleshooting**
- **Model Not Found**: Verify the model name with `ollama list` .
- **Permission Issues**: Use `sudo` for Docker commands or adjust user group permissions .
- **Performance Issues**: Reduce the model size or enable GPU support .

---

### **Example Workflow**
```bash
# Start Ollama container with GPU
docker run -d --gpus all -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama

# Pull and run the 7B model
docker exec -it ollama ollama run deepseek-r1:7b

# Test via API
curl http://localhost:11434/api/generate -d '{"model": "deepseek-r1:7b", "prompt": "Explain quantum computing"}'
```

This setup ensures isolation, portability, and flexibility. For advanced use cases (e.g., fine-tuning), refer to Ollama’s documentation or community guides .
