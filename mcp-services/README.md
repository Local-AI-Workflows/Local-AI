# MCP Services - Model Context Protocol Implementations

This directory contains custom implementations of the Anthropic Model Context Protocol (MCP), providing tools and services for integration with Large Language Models.

## 📋 Services Overview

### 1. Weather Service
**Purpose:** Provides real-time weather data through MCP

**Features:**
- Current weather conditions
- Location-based forecasts
- Powered by OpenWeather API

**Files:**
- `weather-service/src/weather_server.py` - Server implementation
- `weather-service/run_server.py` - Entry point

**Configuration:**
```env
OPENWEATHER_API_KEY=your_api_key_here
```

Get your API key from: https://openweathermap.org/api

### 2. Time Service
**Purpose:** Provides timezone-aware time and date information

**Features:**
- Current time in any timezone
- Timezone information and conversion
- DST-aware calculations
- ZoneInfo support

**Files:**
- `time-service/src/time_server.py` - Server implementation
- `time-service/run_server.py` - Direct execution
- `time-service/sse_server.py` - Server-Sent Events variant
- `time-service/openwebui_function.py` - OpenWebUI integration

**Configuration:**
```env
LOCAL_TIMEZONE=Europe/Berlin
```

### 3. Mensa Service
**Purpose:** Provides HTWG campus mensa menu information

**Features:**
- Menu data for campus dining facility
- Meal categories and pricing
- Information parsing with BeautifulSoup

**Files:**
- `mensa-service/src/mensa_server.py` - Server implementation
- `mensa-service/run_server.py` - Entry point

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- Docker & Docker Compose
- Environment variables configured (see Configuration section)

### Option 1: Docker Compose (Recommended)

```bash
# 1. Configure environment
cp .env.example .env
# Edit .env with your API keys

# 2. Start all services
docker-compose up -d

# 3. Verify services are running
docker-compose ps
```

Services will be available at:
- Weather Service: `http://localhost:8001/sse`
- Time Service: `http://localhost:8002/sse`
- Mensa Service: `http://localhost:8003/sse`

### Option 2: Local Execution

```bash
# Install dependencies
pip install -r requirements.txt

# Run individual services
python weather-service/run_server.py
python time-service/run_server.py
python mensa-service/run_server.py
```

## 📁 Service Architecture

Each service follows a standard MCP structure:

```
service-name/
├── src/
│   └── [service]_server.py    # Core MCP server implementation
├── run_server.py               # Entry point for direct execution
└── tests/                      # Unit tests (optional)
```

### Service Implementation Pattern

Each service implements:
1. **MCP Server** - Handles MCP protocol communication
2. **Tool Definitions** - Describes available functions for LLMs
3. **Tool Handlers** - Implements the actual tool logic

Example:
```python
@server.call_tool()
async def handle_call_tool(name: str, arguments: dict) -> list[TextContent]:
    if name == "get_weather":
        # Implement weather retrieval
        pass
```

## 🔌 Docker Compose Architecture

The `docker-compose.yml` orchestrates services with proxy layers:

```
┌─────────────────────────────────────────┐
│      MCPO HTTP Proxy Layer              │
├─────────────────────────────────────────┤
│ • Weather Service (Port 8001)           │
│ • Time Service (Port 8002)              │
│ • Mensa Service (Port 8003)             │
├─────────────────────────────────────────┤
│      MCP Server Services                │
├─────────────────────────────────────────┤
│ • FastAPI + SSE Transport               │
│ • Pydantic Data Validation              │
└─────────────────────────────────────────┘
```

**MCPO (MCP-to-OpenAPI Proxy):** Converts MCP protocol communication to HTTP/REST for easier integration.

## 📊 Configuration

### Environment Variables

Create `.env` file in this directory:

```env
# OpenWeather API
OPENWEATHER_API_KEY=your_openweather_api_key

# Time Service
LOCAL_TIMEZONE=Europe/Berlin

# Logging
LOG_LEVEL=INFO

# Service Ports
PORT=8000

# CORS
CORS_ORIGINS=*
```

### Logging Configuration

Control logging detail with `LOG_LEVEL`:
- `DEBUG` - Verbose output, includes all request/response details
- `INFO` - Standard operation logs
- `WARNING` - Only warnings and errors
- `ERROR` - Only errors

## 🧪 Testing Services

### Using curl

```bash
# Test weather service (via SSE)
curl http://localhost:8001/sse

# Test time service
curl http://localhost:8002/sse

# Test mensa service
curl http://localhost:8003/sse
```

### Using Python

```python
import httpx
import json

async def test_weather():
    async with httpx.AsyncClient() as client:
        response = await client.get("http://localhost:8001/sse")
        async for line in response.aiter_lines():
            if line.startswith("data: "):
                print(json.loads(line[6:]))
```

## 🔧 Service Management

### Start Services
```bash
docker-compose up -d
```

### Stop Services
```bash
docker-compose down
```

### View Logs
```bash
docker-compose logs -f [service-name]
# Example: docker-compose logs -f weather-service
```

### Restart a Service
```bash
docker-compose restart [service-name]
```

### Rebuild Services
```bash
docker-compose build --no-cache
```

Alternatively, use the management script:
```bash
./manage.sh start      # Start all services
./manage.sh stop       # Stop all services
./manage.sh restart    # Restart all services
./manage.sh logs       # View logs
```

## 📝 Adding a New Service

To add a new MCP service:

1. **Create service directory**
   ```bash
   mkdir new-service
   mkdir new-service/src
   ```

2. **Implement server** (`new-service/src/new_server.py`)
   ```python
   from mcp.server import Server
   from mcp.server.sse import SseServerTransport

   server = Server("new-service")

   @server.call_tool()
   async def handle_call_tool(name: str, arguments: dict):
       # Implement tools
       pass
   ```

3. **Create entry point** (`new-service/run_server.py`)
   ```python
   from src.new_server import server

   async def main():
       async with SseServerTransport(server) as transport:
           await transport.run()
   ```

4. **Add to docker-compose.yml**
   ```yaml
   new-service:
     build: ./new-service
     ports:
       - "8004:8000"
     environment:
       - LOG_LEVEL=INFO
   ```

## 🐛 Troubleshooting

### Services fail to start
- Check that required ports (8001-8003) are available
- Verify environment variables are set correctly
- Check logs: `docker-compose logs [service-name]`

### Connection refused errors
- Ensure services are running: `docker-compose ps`
- Check service ports match docker-compose configuration
- Verify network connectivity with `curl http://localhost:8001/health`

### API key errors
- Verify OPENWEATHER_API_KEY is set in .env
- Check API key is valid and has required permissions
- Ensure .env file is in the mcp-services directory

### Timezone issues
- Verify LOCAL_TIMEZONE value is valid (e.g., Europe/Berlin)
- Use `python -c "import zoneinfo; print(list(zoneinfo.available_timezones()))"` to list valid zones

## 📚 Additional Resources

- [MCP Documentation](https://modelcontextprotocol.io/)
- [Anthropic MCP SDK](https://github.com/anthropics/protocols)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [OpenWeather API](https://openweathermap.org/api)

## 📋 Deployment

For deployment to production servers at HTWG Konstanz:

1. **Clone repository** on target server
2. **Install Docker & Docker Compose**
3. **Configure .env** with production API keys
4. **Run services** with `docker-compose up -d`
5. **Use reverse proxy** (nginx/Apache) for external access

See [../SETUP.md](../SETUP.md) for full deployment guide.

## 👥 Contributing

See [../CONTRIBUTING.md](../CONTRIBUTING.md) for guidelines on contributing new services or improvements.

---

**Last Updated:** 2025-03-02 | **Status:** Active Development
