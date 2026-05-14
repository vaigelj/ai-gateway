# AI Gateway

A fork of [envoyproxy/ai-gateway](https://github.com/envoyproxy/ai-gateway) — an Envoy-based gateway for routing and managing AI/LLM traffic.

## Overview

AI Gateway provides a unified interface for routing requests to multiple AI providers (OpenAI, Anthropic, Google Gemini, Ollama, etc.) with features like:

- **Unified API**: Single endpoint for multiple LLM backends
- **Load balancing**: Distribute requests across providers and models
- **Rate limiting**: Per-user and per-model rate controls
- **Observability**: Metrics, tracing, and logging via Envoy
- **Local models**: First-class support for Ollama and other local inference servers

## Prerequisites

- Go 1.22+
- Envoy proxy (see [`.envoy-version`](.envoy-version) for required version)
- Docker (optional, for containerized deployments)

## Quick Start

### Running with Ollama (local models)

1. Start Ollama:
   ```bash
   ollama serve
   ollama pull llama3
   ```

2. Configure environment:
   ```bash
   cp .env.ollama .env
   # Edit .env as needed
   ```

3. Start the gateway:
   ```bash
   make run
   ```

### Running with cloud providers

```bash
export OPENAI_API_KEY=sk-...
export ANTHROPIC_API_KEY=sk-ant-...
make run
```

## Configuration

The gateway is configured via Envoy's xDS API or static configuration files. See the [`config/`](config/) directory for examples.

## Development

```bash
# Install dependencies
go mod download

# Run tests
make test

# Build
make build

# Lint
make lint
```

## Architecture

```
Client → Envoy (AI Gateway filter) → LLM Provider
                    ↓
            External Processor
            (Go control plane)
```

The Go control plane implements an Envoy [External Processing (ext_proc)](https://www.envoyproxy.io/docs/envoy/latest/configuration/http/http_filters/ext_proc_filter) filter that:

1. Inspects incoming requests
2. Selects the appropriate backend based on routing rules
3. Transforms requests to match the target provider's API format
4. Handles response streaming and transformation

## Contributing

Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

Apache 2.0 — see [LICENSE](LICENSE) for details.
