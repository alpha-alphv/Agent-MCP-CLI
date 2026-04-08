# Agent MCP CLI

A flexible, multi-provider LLM chatbot framework powered by the **Model Context Protocol (MCP)**. Connect to OpenAI, Anthropic, Groq, Gemini, Ollama, and more, then extend them with real-world tools like web search, GitHub, Gmail, and browser automation.

---

## Features

| Feature | Description |
|---|---|
| Multi-Provider Support | Switch between OpenAI, Anthropic, Groq, Gemini, Ollama, and OpenRoute |
| Dual Interface | CLI (`ChatMCP.py`) or web UI (`FlaskMCP.py`) |
| MCP Tool Ecosystem | Web search, GitHub, Gmail, browser automation, and more |
| Custom Tool Support | Plug in any MCP-compatible server |
| Resilient Architecture | Circuit breakers, retries, health checks, graceful degradation |
| Async Core | Concurrent connections and operations via asyncio |
| Token Tracking | Monitor token usage per request across providers |
| Performance Metrics | Response time and token counts per interaction |

---

## Getting Started

### Prerequisites

- Python 3.8+
- Node.js (for NPM-based MCP servers)
- API keys for desired LLM providers

### Installation

**1. Clone the repository**
```bash
git clone
cd
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Configure environment variables**

Create a `.env` file:
```env
# LLM API Keys
ANTHROPIC_API_KEY=your_anthropic_key
OPENAI_API_KEY=your_openai_key
GROQ_API_KEY=your_groq_key
GEMINI_API_KEY=your_gemini_key
OPENROUTE_API_KEY=your_openroute_key
GITHUB_API_KEY=your_github_token

# Ollama (local models)
OLLAMA_HOST=http://localhost:11434

# Default provider and model
DEFAULT_LLM_PROVIDER=groq
DEFAULT_LLM_MODEL=llama-3.2-90b-vision-preview

# Optional tuning
CONNECTION_TIMEOUT=10
READ_TIMEOUT=60
MAX_RETRIES=3
RETRY_DELAY_BASE=1.0
RETRY_MAX_DELAY=30.0
MESSAGE_HISTORY_LIMIT=20
```

**4. Configure MCP servers**

Edit `servers_config.json`:
```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": { "BRAVE_API_KEY": "your_brave_api_key" }
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "your_github_token" }
    },
    "fetch": {
      "command": "uvx",
      "args": ["mcp-server-fetch"]
    },
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    },
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    },
    "gmail": {
      "command": "npx",
      "args": ["-y", "@gongrzhe/server-gmail-autoauth-mcp"]
    }
  }
}
```

---

## Usage

### CLI

```bash
python ChatMCP.py
```

#### Available Commands

| Command | Description |
|---|---|
| `/llm` | List available providers and models |
| `/switch <provider> <model>` | Switch LLM (e.g. `/switch openai gpt-4o`) |
| `/refresh` | Refresh available model lists |
| `/tools` | Show connected MCP tools |
| `/resources` | Show available MCP resources |
| `/prompts` | Show available MCP prompts |
| `/now` | Show current provider, model, and token usage |
| `/help` | Show help |
| `quit` / `exit` | Exit the chat |

## Architecture

```
ChatSession
├── Server (×N)          — MCP server connections
│   ├── CircuitBreaker   — Prevents cascading failures
│   └── AsyncRetry       — Exponential backoff with jitter
└── LLMClient            — Multi-provider HTTP client
    ├── ProviderRegistry — Manages provider configs
    └── TokenUsage       — Tracks token consumption
```

- **Server**: Manages MCP server lifecycle, tool/resource/prompt caching, and health checks
- **LLMClient**: Routes requests to the active provider, handles model discovery and failover
- **ChatSession**: Orchestrates message flow between user, LLM, and tools via async queues

---

## Supported Providers

| Provider | Models |
|---|---|
| OpenAI | GPT-4o, GPT-4 Turbo, GPT-3.5 Turbo |
| Anthropic | Claude 3.5 Sonnet, Claude 3 Opus |
| Groq | Llama 3.2 90B, Llama 3.2 70B |
| Google Gemini | Gemini 1.5 Pro, Gemini 1.5 Flash |
| OpenRoute | All OpenRouter-supported models |
| Ollama | Any locally installed model |
