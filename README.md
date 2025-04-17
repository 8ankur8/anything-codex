# Anything-Codex CLI

<p align="center">A lightweight, provider-agnostic coding agent that runs in your terminal</p>

<p align="center"><code>npm i -g @xai/anything-codex</code></p>

![alt text](./.github/demo.gif)

## Table of Contents
- [Experimental Technology Disclaimer](#experimental-technology-disclaimer)
- [Quickstart](#quickstart)
- [Why Anything-Codex?](#why-anything-codex)
- [Supported Providers](#supported-providers)
- [Security Model & Permissions](#security-model--permissions)
- [System Requirements](#system-requirements)
- [CLI Reference](#cli-reference)
- [Installation](#installation)
- [Configuration](#configuration)
- [Contributing](#contributing)
- [License](#license)

## Experimental Technology Disclaimer

Anything-Codex CLI is an experimental, open-source project under active development. It may contain bugs, incomplete features, or breaking changes. We’re building it transparently with the community and welcome:
- Bug reports
- Feature requests
- Pull requests for new providers or features
- Good vibes

Help us improve by filing issues or submitting PRs (see [Contributing](#contributing))!

## Quickstart

Install globally:

```bash
npm install -g @xai/anything-codex
```

Set up your provider API keys in `~/.anything-codex/config.yaml`:

```yaml
providers:
  openai:
    apiKey: "your-openai-api-key"
  ollama:
    endpoint: "http://localhost:11434"
  anthropic:
    apiKey: "your-anthropic-api-key"
defaultProvider: openai
defaultModel: gpt-4o-mini
```

Run interactively:

```bash
anything-codex
```

Or with a prompt:

```bash
anything-codex "create a todo-list app in React"
anything-codex --provider ollama --model llama3.1 "explain this codebase"
```

Anything-Codex will scaffold files, run code in a sandbox, install dependencies, and show results. Approve changes to commit them to your repo.

## Why Anything-Codex?

Anything-Codex is a terminal-based coding agent that supports *any* AI provider, from OpenAI to local models like Ollama. Built for developers who live in the terminal, it offers:
- **Provider-Agnostic**: Use OpenAI, Anthropic, Ollama, OpenRouter, Google, Mistral, DeepSeek, Azure OpenAI, and more.
- **Zero Setup**: Configure your API keys, and it just works.
- **Secure Sandboxing**: Runs code in a network-disabled, directory-confined sandbox.
- **Open-Source**: Fully transparent, with contributions welcome!

## Supported Providers

| Provider      | Models Supported          | Configuration Key |
|---------------|---------------------------|-------------------|
| OpenAI        | GPT-4o, GPT-4o-mini      | `openai.apiKey`   |
| Ollama        | Llama3.1, Mistral, etc.  | `ollama.endpoint` |
| Anthropic     | Claude 3.5 Sonnet        | `anthropic.apiKey`|
| OpenRouter    | Various models           | `openrouter.apiKey`|
| Google        | Gemini, Vertex AI        | `google.apiKey`   |
| Mistral       | Mixtral, Codestral       | `mistral.apiKey`  |
| DeepSeek      | DeepSeek R-1, etc.       | `deepseek.apiKey` |
| Azure OpenAI  | GPT-4o, etc.             | `azure.apiKey`    |

Add new providers by implementing the `Provider` interface (see [Contributing](#contributing)).

## Security Model & Permissions

Anything-Codex uses the same sandboxing as Codex CLI:
- **macOS**: Apple Seatbelt (`sandbox-exec`) with read-only jails.
- **Linux**: Docker containers with `iptables` to block outbound traffic (except provider APIs).
- **Approval Modes**:
  - `suggest` (default): Requires approval for writes and commands.
  - `auto-edit`: Auto-applies file patches, requires approval for commands.
  - `full-auto`: Auto-executes all actions (network-disabled).

Set via `--approval-mode` or `config.yaml`.

## System Requirements

- **OS**: macOS 12+, Ubuntu 20.04+/Debian 10+, Windows 11 via WSL2
- **Node.js**: 22+ (LTS recommended)
- **Git**: 2.23+ (optional, recommended)
- **RAM**: 4GB minimum (8GB recommended)

## CLI Reference

| Command                              | Purpose                           | Example                                      |
|--------------------------------------|-----------------------------------|----------------------------------------------|
| `anything-codex`                     | Interactive REPL                  | `anything-codex`                             |
| `anything-codex "prompt"`            | Run with initial prompt           | `anything-codex "fix lint errors"`           |
| `anything-codex -q "prompt"`         | Non-interactive mode              | `anything-codex -q "explain utils.ts"`       |
| `anything-codex --provider <name>`   | Specify provider                  | `anything-codex --provider ollama "task"`    |

Flags: `--provider`, `--model`, `--approval-mode`, `--quiet`.

## Installation

```bash
npm install -g @xai/anything-codex
```

Or build from source:

```bash
git clone https://github.com/xai/anything-codex.git
cd anything-codex
npm install
npm run build
npm link
```

## Configuration

Configure in `~/.anything-codex/config.yaml`:

```yaml
providers:
  openai:
    apiKey: "sk-..."
  ollama:
    endpoint: "http://localhost:11434"
defaultProvider: openai
defaultModel: gpt-4o-mini
approvalMode: suggest
```

Custom instructions in `~/.anything-codex/instructions.md`:

```markdown
- Use TypeScript for all code generation.
- Avoid external API calls unless specified.
```

## Contributing

We welcome contributions! To get started:
1. Fork the repo and create a topic branch (e.g., `feat/new-provider`).
2. Run `npm install` and `npm run test:watch`.
3. Implement your changes with tests (use Vitest).
4. Sign the CLA by commenting in your PR:
   ```
   I have read the CLA Document and I hereby sign the CLA
   ```
5. Run `npm test && npm run lint && npm run typecheck` before pushing.
6. Open a PR with a clear description.

See [CONTRIBUTING.md](./CONTRIBUTING.md) for details.

## License

Licensed under the Apache-2.0 License.
