# Infrastructure Setup & Deployment Guide

Complete guide for setting up and deploying the LLM Workflows project.

## 📋 Table of Contents

1. [Local Development Setup](#local-development-setup)
2. [Docker & Containerization](#docker--containerization)
3. [MCP Services Deployment](#mcp-services-deployment)
4. [Production Deployment](#production-deployment)
5. [Infrastructure Overview](#infrastructure-overview)
6. [Troubleshooting](#troubleshooting)

## 🖥️ Local Development Setup

### Requirements

- **Python:** 3.8 or higher
- **Docker:** Latest stable version
- **Docker Compose:** Latest stable version
- **Git:** For version control
- **Text Editor/IDE:** VS Code, PyCharm, or similar

### Step 1: Clone Repository

```bash
git clone https://github.com/<organization>/Local-AI.git
cd Local-AI
```

### Step 2: Create Python Virtual Environment

```bash
# Create virtual environment
python -m venv venv

# Activate (Linux/macOS)
source venv/bin/activate

# Activate (Windows)
venv\Scripts\activate
```

### Step 3: Install Python Dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### Step 4: Configure Environment

```bash
# Navigate to MCP services
cd mcp-services

# Copy environment template
cp .env.example .env

# Edit with your settings
# - Add OPENWEATHER_API_KEY
# - Set LOCAL_TIMEZONE
# - Configure LOG_LEVEL if needed
```

Edit `.env`:
```env
OPENWEATHER_API_KEY=your_key_here
LOCAL_TIMEZONE=Europe/Berlin
LOG_LEVEL=INFO
```

### Step 5: Start Services

```bash
# From mcp-services directory
docker-compose up -d

# Verify services are running
docker-compose ps

# View logs
docker-compose logs -f
```

### Step 6: Verify Installation

```bash
# Test weather service
curl http://localhost:8001/sse

# Test time service
curl http://localhost:8002/sse

# Test mensa service
curl http://localhost:8003/sse
```

## 🐳 Docker & Containerization

### Docker Compose Structure

```yaml
services:
  weather-service:
    build: ./weather-service
    ports:
      - "8001:8000"
    environment:
      - OPENWEATHER_API_KEY=${OPENWEATHER_API_KEY}
      - LOG_LEVEL=${LOG_LEVEL}

  time-service:
    build: ./time-service
    ports:
      - "8002:8000"
    environment:
      - LOCAL_TIMEZONE=${LOCAL_TIMEZONE}
      - LOG_LEVEL=${LOG_LEVEL}

  mensa-service:
    build: ./mensa-service
    ports:
      - "8003:8000"
    environment:
      - LOG_LEVEL=${LOG_LEVEL}
```

### Building Docker Images

```bash
# Build all services
docker-compose build

# Build specific service
docker-compose build weather-service

# Build without cache (fresh build)
docker-compose build --no-cache
```

### Managing Containers

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# Stop and remove volumes
docker-compose down -v

# View logs
docker-compose logs -f [service-name]

# Execute command in container
docker-compose exec weather-service bash
```

## 🔌 MCP Services Deployment

### Architecture

Each service runs as a Docker container with:
- **FastAPI** web framework
- **MCP SDK** for protocol implementation
- **SSE Transport** for Server-Sent Events
- **MCPO Proxy** layer for HTTP compatibility

### Service Configuration

#### Weather Service
- **Port:** 8001
- **Required:** OPENWEATHER_API_KEY
- **Timeout:** 30 seconds (API calls)

#### Time Service
- **Port:** 8002
- **Configuration:** LOCAL_TIMEZONE
- **Features:** Timezone conversion, DST awareness

#### Mensa Service
- **Port:** 8003
- **No external API required**
- **Data source:** Web scraping

### Health Checks

```bash
# Check service health
curl -s http://localhost:8001/health || echo "Service down"

# Monitor service logs in real-time
docker-compose logs -f weather-service

# Get service status
docker-compose ps
```

### Scaling Services

For multiple instances of a service:

```yaml
# docker-compose.yml
weather-service-1:
  image: local-ai/weather-service
  ports:
    - "8001:8000"

weather-service-2:
  image: local-ai/weather-service
  ports:
    - "8004:8000"
```

Then use load balancing (nginx) in front.

## 🚀 Production Deployment

### HTWG Konstanz Servers

The project is designed to run on three dedicated servers:

| Server | Hardware | Role |
|--------|----------|------|
| Ollama Workstation | Intel Xeon E5-2667v4, 32GB RAM, NVIDIA Quadro P6000 | LLM inference |
| Atlas Server | 2× Xeon E5-2630v3, 128GB RAM, dual NVIDIA GPUs | High-performance computing |
| GPU-PC01 | AMD Ryzen 9 5900X, 32GB RAM, RTX 3080 Ti | Benchmarking & evaluation |

### Deployment Steps

#### 1. Prepare Production Environment

```bash
# SSH into target server
ssh user@server.htwg-konstanz.de

# Install Docker (if not present)
sudo apt-get update
sudo apt-get install docker.io docker-compose

# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker

# Clone repository
git clone https://github.com/<org>/Local-AI.git
cd Local-AI
```

#### 2. Configure Production Environment

```bash
# Create secure .env file
cd mcp-services
cp .env.example .env

# Use secure values (e.g., from secrets management)
# - Use strong API keys
# - Set appropriate LOG_LEVEL (WARNING or ERROR for production)
# - Configure CORS_ORIGINS for allowed domains
```

#### 3. Deploy Services

```bash
# Build images on server
docker-compose build

# Start services as daemon
docker-compose up -d

# Verify deployment
docker-compose ps
curl http://localhost:8001/sse
```

#### 4. Setup Reverse Proxy

Example nginx configuration:

```nginx
upstream mcp_weather {
    server localhost:8001;
}

upstream mcp_time {
    server localhost:8002;
}

upstream mcp_mensa {
    server localhost:8003;
}

server {
    listen 80;
    server_name llm-api.htwg-konstanz.de;

    location /weather {
        proxy_pass http://mcp_weather;
    }

    location /time {
        proxy_pass http://mcp_time;
    }

    location /mensa {
        proxy_pass http://mcp_mensa;
    }
}
```

#### 5. Enable SSL/TLS

```bash
# Install Let's Encrypt certbot
sudo apt-get install certbot python3-certbot-nginx

# Obtain certificate
sudo certbot --nginx -d llm-api.htwg-konstanz.de

# Auto-renewal is typically configured by certbot
```

#### 6. Setup Monitoring (Optional)

```bash
# Monitor service logs
docker-compose logs -f

# Setup log aggregation (if needed)
# Example: ELK stack or Grafana
```

### Maintenance

#### Regular Backups

```bash
# Backup important data
docker-compose exec weather-service tar -czf backup.tar.gz /data

# Restore from backup
docker cp backup.tar.gz <container>:/
docker-compose exec weather-service tar -xzf /backup.tar.gz
```

#### Updates

```bash
# Check for updates
git fetch origin
git log --oneline origin/main..main

# Pull latest changes
git pull origin main

# Rebuild and restart services
docker-compose build
docker-compose up -d
```

#### Monitoring Resource Usage

```bash
# View resource stats
docker stats

# Check disk usage
df -h

# Monitor memory
free -h
```

## 🏗️ Infrastructure Overview

### Network Architecture

```
┌─────────────────────────────────┐
│     External Internet            │
└────────────────┬────────────────┘
                 │
         ┌───────────────────┐
         │  Reverse Proxy    │
         │   (nginx/Apache)  │
         └────────┬──────────┘
                  │
    ┌─────────────┼─────────────┐
    │             │             │
    ▼             ▼             ▼
┌────────┐  ┌────────┐  ┌────────┐
│Weather │  │  Time  │  │ Mensa  │
│Service │  │Service │  │Service │
└────────┘  └────────┘  └────────┘
```

### Bandwidth Considerations

- **Weather API:** ~2KB per request
- **Time Service:** ~500B per request
- **Mensa Service:** ~5KB per request
- **Expected load:** Reasonable for academic use

### Storage Requirements

- **Container images:** ~500MB (all services)
- **Logs (per service):** ~10MB per day
- **API caches:** Minimal (services are stateless)

## 🔐 Security Considerations

### API Keys

- Store API keys in `.env`, never in code
- Rotate keys periodically
- Use `.env.example` for non-sensitive defaults
- Ensure `.env` is in `.gitignore`

### Environment

- Keep Docker images updated
- Use HTTPS in production
- Limit CORS origins appropriately
- Monitor access logs for anomalies

### Secrets Management (For Production)

Consider using:
- HashiCorp Vault
- AWS Secrets Manager
- Kubernetes Secrets
- Docker Secrets (for Swarm)

Example with environment variables:
```bash
export OPENWEATHER_API_KEY=$(vault kv get -field=api_key secret/openweather)
docker-compose up -d
```

## 🔧 Troubleshooting

### Services Won't Start

```bash
# Check Docker daemon
docker ps

# View error logs
docker-compose logs [service-name]

# Check port conflicts
netstat -an | grep 8001-8003

# Verify .env file
cat .env
```

### API Key Errors

```bash
# Verify OPENWEATHER_API_KEY is set
echo $OPENWEATHER_API_KEY

# Check .env is being read
docker-compose config | grep OPENWEATHER

# Recreate containers with new env
docker-compose down
docker-compose up -d
```

### Timezone Issues

```bash
# List available timezones
timedatectl list-timezones

# Set system timezone
timedatectl set-timezone Europe/Berlin

# Verify time service
curl http://localhost:8002/sse | head -20
```

### Network/Connectivity

```bash
# Test container network
docker network ls
docker network inspect [network-name]

# Ping services from host
curl http://localhost:8001/health

# Test from container
docker-compose exec weather-service curl http://time-service:8000/health
```

### High CPU/Memory Usage

```bash
# Monitor resource usage
docker stats

# Check service logs for errors
docker-compose logs [service-name]

# Restart problematic service
docker-compose restart [service-name]

# Check for resource limits needed
# Adjust docker-compose memory limits if necessary
```

## 📞 Support

For deployment issues:
1. Check Troubleshooting section above
2. Review service-specific README files
3. Check Docker Compose and service logs
4. Contact project supervisors

---

**Last Updated:** 2025-03-02 | **Maintained by:** LLM Workflows Team
