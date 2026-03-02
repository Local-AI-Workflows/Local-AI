# Contributing to LLM Workflows Project

Thank you for your interest in contributing to the LLM Workflows project! This document provides guidelines for team members and potential contributors.

## 📋 Code of Conduct

This is an academic project at HTWG Konstanz. All team members are expected to:
- Maintain professional and respectful communication
- Support collaborative learning and development
- Share knowledge and experiences with the team
- Document work for team transparency

## 🎯 Project Goals

Our project focuses on:
- Practical exploration of Model Context Protocol (MCP) with LLMs
- Building and benchmarking LLM evaluation frameworks
- Creating useful tools through MCP services
- Documenting findings and best practices

## 🚀 Getting Started

### Setup Development Environment

```bash
# Clone the repository
git clone <repository-url>
cd Local-AI

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set up environment variables
cp mcp-services/.env.example mcp-services/.env
# Edit .env with your configuration
```

### Project Structure

- `mcp-services/` - MCP service implementations
- `LLM Workflows Teamprojekt/` - Presentation and documentation
- `docs/` - Additional documentation
- Root `.py` files - Benchmarking and evaluation scripts

## 🔄 Workflow

### 1. Before Starting Work

- Check the TODO lists in documentation
- Discuss significant changes with the team or supervisors
- Create or comment on relevant issues if using issue tracking

### 2. Making Changes

#### Branch Naming
Create feature branches with clear names:
```bash
git checkout -b feature/description-of-feature
git checkout -b fix/description-of-issue
git checkout -b docs/documentation-update
```

#### Commit Messages
Write clear, descriptive commit messages:
```
feat: Add weather service MCP implementation
fix: Handle timezone edge case in time service
docs: Update README with deployment instructions
refactor: Simplify benchmarking framework
```

Format: `type: description`

Types:
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation
- `refactor` - Code refactoring
- `test` - Test additions/changes
- `chore` - Build, dependencies, configuration

#### Code Style

**Python:**
- Follow PEP 8 style guide
- Use type hints for function signatures
- Include docstrings for classes and public methods
- Max line length: 100 characters

**Example:**
```python
def get_weather(location: str, unit: str = "celsius") -> dict[str, Any]:
    """
    Fetch weather data for a location.

    Args:
        location: City name or coordinates
        unit: Temperature unit (celsius/fahrenheit)

    Returns:
        Dictionary containing weather information
    """
    # Implementation
    pass
```

**Markdown:**
- Use consistent heading levels
- Include code examples where helpful
- Maintain clear structure and readability

### 3. Testing

Before committing changes:

```bash
# Run Python linter (if configured)
python -m pylint mcp-services/

# Test imports
python -c "import mcp_benchmark_llm; print('OK')"

# For MCP services, test locally
docker-compose -f mcp-services/docker-compose.yml up --build
```

### 4. Documentation

Update documentation for:
- New features or services
- Configuration changes
- Deployment procedures
- Troubleshooting steps

Documentation locations:
- Service docs: `mcp-services/README.md`
- Setup guide: `SETUP.md`
- Main README: `README.md`

### 5. Pull Requests / Merging

Before merging significant changes:

1. **Self-review** - Check your changes
2. **Test locally** - Verify functionality
3. **Update docs** - Add/update documentation
4. **Get team review** - For significant changes
5. **Merge to main** - When approved

## 📝 Adding Features

### Adding a New MCP Service

1. Create service directory
   ```bash
   mkdir mcp-services/new-service
   mkdir mcp-services/new-service/src
   ```

2. Implement service in `src/new_service.py`
   ```python
   from mcp.server import Server
   from mcp.server.sse import SseServerTransport

   server = Server("new-service")

   @server.call_tool()
   async def handle_call_tool(name: str, arguments: dict):
       # Implementation
       pass
   ```

3. Create `run_server.py` entry point

4. Add to `docker-compose.yml`

5. Update `mcp-services/README.md` with service documentation

6. Create `.env` variables if needed

### Adding a Benchmarking Test

1. Add test case to `mcp_benchmark_llm.py`
2. Follow existing test patterns
3. Document expected results
4. Add to evaluation suite

### Updating Presentation

1. Edit `LLM Workflows Teamprojekt/slides.md`
2. Add components to `LLM Workflows Teamprojekt/components/` if needed
3. Test presentation: `npm run dev`
4. Rebuild if needed: `npm run build`

## 📊 Documentation Standards

### README Files

Every major directory should have a README.md covering:
- Purpose and overview
- Installation/setup instructions
- Configuration
- Usage examples
- Troubleshooting

### Inline Documentation

- Use type hints consistently
- Document function parameters
- Explain non-obvious logic
- Include links to external resources

### Commit History

Keep commit history clean:
- One feature or fix per commit
- Meaningful messages
- Don't commit debug code

## 🐛 Reporting Issues

If you find bugs or issues:

1. **In GitHub Issues:**
   - Clear title describing the problem
   - Steps to reproduce
   - Expected vs. actual behavior
   - Environment details

2. **In code comments:**
   ```python
   # TODO: Optimize this O(n²) algorithm
   # BUG: Timezone calculation fails for DST transitions
   ```

## 🔍 Code Review Checklist

Before submitting code for review:

- [ ] Code follows project style guide
- [ ] All functions have docstrings
- [ ] Type hints present for function signatures
- [ ] Commit messages are clear and descriptive
- [ ] Documentation is updated
- [ ] Code tested locally
- [ ] No debugging code or print statements left
- [ ] Environment variables documented in `.env.example`
- [ ] Breaking changes documented

## 📚 Reference Materials

- [Python PEP 8 Style Guide](https://www.python.org/dev/peps/pep-0008/)
- [MCP Documentation](https://modelcontextprotocol.io/)
- [Anthropic Claude Documentation](https://docs.anthropic.com/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)

## 🎓 Learning Resources

Team members new to relevant technologies:
- MCP: Start with [mcp-services/README.md](mcp-services/README.md)
- LLMs: Review `LLM Workflows Teamprojekt/kontext.md`
- Benchmarking: Study `mcp_benchmark_llm.py` comments

## 👥 Team Contacts

**Project Supervisors:**
- Prof. Matthias Franz
- Prof. Oliver Dürr

**Team Lead:** (See project documentation)

## 📝 License

By contributing, you agree that your contributions will be licensed under the same license as the project. See LICENSE file.

## ❓ Questions?

- Check existing documentation
- Review similar implementations
- Ask team members
- Document interesting findings for future reference

---

Thank you for contributing to making this project better!

**Last Updated:** 2025-03-02
