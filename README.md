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

## Project Structure
```
.
├── Dockerfile           # Python 3.12 base image with dependencies
├── docker-compose.yml   # Service configuration
├── .dockerignore        # Excludes unnecessary files
├── .gitignore          # Git ignore patterns
└── README.md           # This file
```

## License
This Docker setup is provided as-is. PyPotteryInk is licensed under its own terms - see the [original repository](https://github.com/lrncrd/PyPotteryInk).