# PyPotteryInk Docker

Production-ready Docker setup for [PyPotteryInk](https://github.com/lrncrd/PyPotteryInk). The container automatically clones and bootstraps the application with zero local setup required.

## Prerequisites
- Docker and Docker Compose v2
- 4GB+ free disk space for models and dependencies

## Quick Start

1. **Clone this repository:**
   ```bash
   git clone https://github.com/lad-sapienza/PyPotteryInk-Docker.git
   cd PyPotteryInk-Docker
   ```

2. **Start the application:**
   ```bash
   docker compose up -d
   ```

3. **Wait for installation** (first run only, ~5-10 minutes):
   ```bash
   docker compose logs -f app
   ```
   Wait until you see: `* Running on http://0.0.0.0:5003`

4. **Access the application:**
   ```
   http://localhost:5001
   ```

## Management

**View logs:**
```bash
docker compose logs -f app
```

**Stop the application:**
```bash
docker compose down
```

**Stop and remove cached data:**
```bash
docker compose down -v
```

**Restart the application:**
```bash
docker compose restart app
```

**Update to latest PyPotteryInk version:**
```bash
docker compose down -v
docker compose up -d
```

## Configuration

### Persistent Storage
Application data (models, dependencies) is stored in a Docker volume named `potteryink_potteryink-data`. This means:
- **First start**: Downloads PyTorch and models (~5-10 minutes)
- **Subsequent starts**: Uses cached data (starts in ~10 seconds)
- **Rebuilds** (`--build`): Only rebuilds Docker image, data persists

### Container Bootstrap
On first run, the container automatically:
1. Clones [PyPotteryInk](https://github.com/lrncrd/PyPotteryInk) from GitHub
2. Runs `python install.py` to set up dependencies and download models
3. Modifies Flask to bind to `0.0.0.0` for external access
4. Starts the application with `python app.py`

### Ports
- **Host**: `5001` → **Container**: `5003`
- Change host port in `docker-compose.yml` if 5001 is in use

## Troubleshooting

**Connection reset on first start?**
The application is still installing dependencies (PyTorch, models, etc.). This is normal and only happens on first run. Check progress:
```bash
docker compose logs -f app
```

**Port 5001 already in use?**
Change the host port in `docker-compose.yml`:
```yaml
ports:
  - "5002:5003"  # or any available port
```

**Application not starting?**
Check container logs for errors:
```bash
docker compose logs app
```

**Want to start fresh?**
Remove all data and restart:
```bash
docker compose down -v
docker compose up -d
```

## GPU Support

### NVIDIA GPU (Linux/Windows)

To enable NVIDIA GPU acceleration:

**Linux (Ubuntu/Debian):**
```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```

**Windows with Docker Desktop:**
1. Install [NVIDIA GPU drivers](https://www.nvidia.com/Download/index.aspx)
2. Install [Docker Desktop for Windows](https://docs.docker.com/desktop/install/windows-install/) with WSL 2 backend
3. Enable WSL 2 integration in Docker Desktop settings
4. GPU support is automatically available (no additional toolkit needed)

**Start with GPU support:**
```bash
docker compose -f docker-compose.yml -f docker-compose.gpu.yml up -d
```

**Verify GPU is detected:**
```bash
docker compose logs app | grep -i "Using device"
# Should show: "Using device: cuda" instead of "cpu"
```

**Verify GPU is detected:**
```bash
docker compose logs app | grep -i "Using device"
# Should show: "Using device: cuda" instead of "cpu"
```

### Apple Silicon (MPS)

**Important:** Docker on macOS cannot access the Metal GPU. For best performance on Apple Silicon:

**Option 1: Native Installation (Recommended)**
```bash
git clone https://github.com/lrncrd/PyPotteryInk.git
cd PyPotteryInk
python install.py
./PyPotteryInk_UNIX.sh
```

**Option 2: Docker (CPU only)**
The current Docker setup works on Apple Silicon but uses CPU only. This is still useful for:
- Consistent environment across teams
- Easy deployment and cleanup
- Isolation from host system

The PyPotteryInk installer will automatically detect and use GPU/MPS if available (native installation only).

## Deployment

### Share with Others
1. Push this repository to GitHub
2. Others can clone and run with:
   ```bash
   git clone https://github.com/lad-sapienza/PyPotteryInk-Docker.git
   cd PyPotteryInk-Docker
   docker compose up -d
   ```

## Project Structure
```
.
├── Dockerfile              # Python 3.12 base image with dependencies
├── docker-compose.yml      # Main service configuration (CPU)
├── docker-compose.gpu.yml  # GPU override (optional)
├── .dockerignore           # Excludes unnecessary files
├── .gitignore              # Git ignore patterns
└── README.md               # This file
```

## License
This Docker setup is provided as-is. PyPotteryInk is licensed under its own terms - see the [original repository](https://github.com/lrncrd/PyPotteryInk).
└── README.md           # This file
```

## License
This Docker setup is provided as-is. PyPotteryInk is licensed under its own terms - see the [original repository](https://github.com/lrncrd/PyPotteryInk).