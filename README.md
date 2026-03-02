# LLM Workflows - Local AI Infrastructure & MCP Services

A comprehensive project for exploring Model Context Protocol (MCP) integration with Large Language Models, featuring benchmarking frameworks, MCP service implementations, and LLM evaluation tools.

**Project Type:** Academic Team Project - HTWG Konstanz (2 Semesters: WS 2024/25 + SS 2025)

## 🎯 Project Overview

This project combines multiple research areas:

- **MCP Integration**: Practical implementations of Anthropic's Model Context Protocol for LLM tool-use
- **LLM Benchmarking**: Comprehensive evaluation framework for multi-model LLM performance
- **MCP Services**: Custom tools including weather, time/timezone, and campus mensa information
- **Local LLM Infrastructure**: Docker-based setup for Ollama and OpenWebUI

## 📁 Repository Structure

```
Local-AI/
├── mcp-services/                    # MCP service implementations
│   ├── weather-service/             # Weather tool using OpenWeather API
│   ├── time-service/                # Timezone-aware time information service
│   ├── mensa-service/               # Campus mensa information service
│   ├── docker-compose.yml           # Service orchestration with MCPO proxies
│   ├── manage.sh                    # Service management script
│   └── .env.example                 # Environment configuration template
│
├── docs/                            # Documentation
│   ├── services/                    # Service documentation
│   └── infrastructure/              # Infrastructure guides
│
├── mcp_benchmark_llm.py             # Advanced LLM benchmarking framework
├── mcp_advanced_demo.py             # Multi-model evaluation demo
├── mcp_evaluator_wrapper.py         # LLM-based evaluation wrapper
├── requirements.txt                 # Python dependencies
└── run_local.sh                     # Local execution script
```

## 🚀 Quick Start

### Prerequisites

- Python 3.8+
- Docker & Docker Compose
- API Key: OpenWeather API (for weather service)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd Local-AI
   ```

2. **Set up Python environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Configure environment**
   ```bash
   cp mcp-services/.env.example mcp-services/.env
   # Edit .env with your API keys and settings
   ```

4. **Start MCP services**
   ```bash
   cd mcp-services
   docker-compose up -d
   ```

## 📚 Project Components

### MCP Services

Custom Model Context Protocol implementations:

- **Weather Service**: Real-time weather data using OpenWeather API
- **Time Service**: Timezone-aware time and date information
- **Mensa Service**: Campus mensa menu and information

See [mcp-services/README.md](mcp-services/README.md) for detailed setup and usage.

### Benchmarking Framework

Comprehensive LLM evaluation:

- `mcp_benchmark_llm.py`: Core benchmarking framework supporting multiple LLM models
- `mcp_advanced_demo.py`: Demonstration of multi-model evaluation capabilities
- `mcp_evaluator_wrapper.py`: LLM-powered evaluation of model outputs

Supports evaluation with:
- OpenAI models (GPT-4, etc.)
- Anthropic Claude
- Local models via Ollama
- LiteLLM for unified API access

### Presentation

The project presentation is maintained in a **separate repository**. It covers project objectives, MCP integration approach, benchmarking methodology, and current results.

> See the dedicated presentation repository for the Slidev-based slides.

## 🛠️ Technology Stack

**Backend:**
- Python 3.x with LiteLLM for LLM abstraction
- Anthropic MCP SDK
- FastAPI for REST APIs
- Pydantic for data validation

**Infrastructure:**
- Docker & Docker Compose
- Ollama for local LLM inference
- OpenWebUI for LLM interface
- MCPO for MCP-to-OpenAPI proxies

## 👥 Team

This is a collaborative academic project overseen by:
- Prof. Matthias Franz
- Prof. Oliver Dürr

## 📋 Environment Configuration

Create `.env` in `mcp-services/` directory:

```env
# Weather Service
OPENWEATHER_API_KEY=your_api_key_here

# Time Service
LOCAL_TIMEZONE=Europe/Berlin

# Logging
LOG_LEVEL=INFO

# Service Configuration
PORT=8000
CORS_ORIGINS=*
```

See [mcp-services/.env.example](mcp-services/.env.example) for complete configuration options.

## 🧪 Running Benchmarks

```bash
python mcp_benchmark_llm.py
```

This will run the comprehensive benchmarking suite. See [SETUP.md](SETUP.md) for detailed configuration options.

## 📖 Documentation

- [SETUP.md](SETUP.md) - Infrastructure and deployment guide
- [CONTRIBUTING.md](CONTRIBUTING.md) - Team guidelines and contribution workflow
- [mcp-services/README.md](mcp-services/README.md) - MCP services documentation
- [docs/](docs/) - Additional documentation

## 📝 License

This project is part of an academic initiative at HTWG Konstanz. See LICENSE file for details.

## 🔗 Useful Links

- [Model Context Protocol](https://modelcontextprotocol.io/)
- [Anthropic MCP SDK](https://github.com/anthropics/protocols)
- [Ollama](https://ollama.ai/)
- [OpenWebUI](https://openwebui.com/)

## 📞 Support & Contact

For questions or issues, please refer to the project documentation or contact the team through the HTWG Konstanz project management system.

---

**Status:** Active Development | **Last Updated:** 2025-03-02
