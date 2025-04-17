
<h1 align="center">Anything Codex</h1>
<p align="center">Your Universal Coding Agent: An AI-powered coding assistant for your terminal, supporting multiple LLM providers.</p>

<p align="center">
  <!-- Replace 'anything-codex' if your npm package name is different -->
  <a href="https://www.npmjs.com/package/anything-codex"><img src="https://badge.fury.io/js/anything-codex.svg" alt="npm version"></a>
  <!-- Ensure LICENSE file exists in your repo -->
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache_2.0-blue.svg" alt="License"></a>
  <!-- Optional: Add later when CI is set up
  <a href="https://github.com/YOUR_GITHUB_USERNAME/anything-codex/actions/workflows/ci.yml"><img src="https://github.com/YOUR_GITHUB_USERNAME/anything-codex/actions/workflows/ci.yml/badge.svg" alt="Build Status"></a>
  -->
</p>

_Demo coming soon!_

<!-- TODO: Replace with an Anything Codex demo GIF -->
![Codex demo GIF using: codex "explain this codebase to me"](./.github/demo.gif)

---

<details>
<summary><strong>Table&nbsp;of&nbsp;Contents</strong></summary>

- [Project Vision & Status](#project-vision--status)
- [Quickstart](#quickstart)
- [Why Anything Codex?](#why-anything-codex)
- [Supported Providers](#supported-providers)
- [Security Model & Permissions](#security-model--permissions)
  - [Platform sandboxing details](#platform-sandboxing-details)
- [System Requirements](#system-requirements)
- [CLI Reference](#cli-reference)
- [Memory & Project Docs](#memory--project-docs)
- [Non‑interactive / CI mode](#noninteractive--ci-mode)
- [Recipes](#recipes)
- [Installation](#installation)
- [Configuration](#configuration)
- [FAQ](#faq)
- [Contributing](#contributing)
  - [Call for Contributors!](#call-for-contributors)
  - [Development workflow](#development-workflow)
  - [Adding New Providers](#adding-new-providers)
  - [Writing high‑impact code changes](#writing-highimpact-code-changes)
  - [Opening a pull request](#opening-a-pull-request)
  - [Review process](#review-process)
  - [Community values](#community-values)
  - [Getting help](#getting-help)
  - [Contributor License Agreement (CLA)](#contributor-license-agreement-cla)
    - [Quick fixes](#quick-fixes)
  - [Releasing `anything-codex`](#releasing-anything-codex)
- [Security & Responsible AI](#security--responsible-ai)
- [License](#license)

</details>

---

## Project Vision & Status

**Anything Codex** aims to be a flexible, powerful, and community-driven coding agent that runs in your terminal. Inspired by the original OpenAI Codex CLI, this project extends the concept to support a wide range of Large Language Model (LLM) providers, including commercial APIs and local models.

**This is an early-stage, experimental project.** We are actively seeking contributors to help build out functionality, add provider support, and shape the future of the tool. Expect bugs, rapid changes, and incomplete features. We're building this in the open and welcome:

-   Bug reports
-   Feature requests (especially provider support!)
-   Pull requests
-   Ideas and discussion

Help us build the ultimate terminal coding assistant!

## Quickstart

Install globally:

```shell
npm install -g anything-codex
```

Next, configure your desired LLM provider(s). You can use environment variables (shown below) or the configuration file (`~/.anything-codex/config.yaml`, recommended for multiple providers).

**Example using OpenAI via environment variable:**

```shell
export OPENAI_API_KEY="your-openai-api-key"
```

**Example using Ollama (local) via environment variable:**

```shell
# Ensure Ollama server is running, e.g., `ollama serve`
export OLLAMA_BASE_URL="http://localhost:11434" # Default Ollama URL
```

> **Note:** Environment variables set this way are only for the current session. Add `export` lines to your shell's configuration file (e.g., `~/.zshrc`, `~/.bashrc`) for persistence. See the [Configuration](#configuration) section for managing multiple providers easily.

Run interactively:

```shell
anything-codex
```

Run with a prompt, specifying the provider and model (if not using defaults):

```shell
anything-codex --provider openai --model gpt-4o "explain this codebase to me"
```

```shell
anything-codex --provider ollama --model llama3 "refactor this function to be more efficient"
```

Run in `Full Auto` mode (use with caution!):

```shell
anything-codex --provider anthropic --model claude-3-opus-20240229 --approval-mode full-auto "create a simple flask todo app"
```

That’s it! Anything Codex will interact with your chosen LLM, scaffold files, run commands inside a sandbox, install dependencies, and show you the results. Approve changes to commit them to your working directory.

---

## Why Anything Codex?

Anything Codex is built for developers who **live in the terminal** and want:

-   **LLM Flexibility:** Choose the best model for the job from various providers (OpenAI, Anthropic, Google, Ollama, etc.) without being locked into a single ecosystem.
-   **ChatGPT-level Reasoning:** Leverage powerful AI models for coding tasks.
-   **Code Execution & File Manipulation:** Go beyond chat – let the agent run code, modify files, and iterate, all within a secure sandbox.
-   **Version Control Integration:** Work seamlessly with Git, approving changes before they hit your working directory.
-   **Zero Setup (almost):** Bring your API keys/endpoints, and it works.
-   **Safety & Security:** Auto-approval modes balanced with network-disabled and directory-sandboxed execution.
-   **Open Source & Community Driven:** See the code, contribute features, and help shape the tool's direction.

It's _chat-driven development_ that understands your repo, powered by the LLM of *your* choice.

---

## Supported Providers

Anything Codex aims to support a wide range of providers. Current status (Help us add more!):

*   [x] **OpenAI** (via API Key)
*   [ ] **Ollama** (via Base URL for local models)
*   [ ] **Anthropic** (Needs Implementation - PRs welcome!)
*   [ ] **Google Gemini** (Needs Implementation - PRs welcome!)
*   [ ] **Mistral AI** (Needs Implementation - PRs welcome!)
*   [ ] **DeepSeek** (Needs Implementation - PRs welcome!)
*   [ ] **Azure OpenAI** (Needs Implementation - PRs welcome!)
*   [ ] **OpenRouter** (Needs Implementation - PRs welcome!)
*   [ ] *Your favourite provider here?* (Open an issue or PR!)

See the [Configuration](#configuration) section for how to set up credentials/endpoints.

---

## Security Model & Permissions

Anything Codex inherits the security-conscious design of the original, letting you control the agent's autonomy via the `--approval-mode` flag:

| Mode                      | What the agent may do without asking            | Still requires approval                                         |
| ------------------------- | ----------------------------------------------- | --------------------------------------------------------------- |
| **Suggest** <br>(default) | • Read any file in the repo                     | • **All** file writes/patches <br>• **All** shell/Bash commands |
| **Auto Edit**             | • Read **and** apply patch writes to files      | • **All** shell/Bash commands                                   |
| **Full Auto**             | • Read/write files <br>• Execute shell commands | –                                                               |

In **Full Auto**, commands run **network-disabled** (except for the LLM API call itself, if applicable) and confined to the current working directory (plus temporary files). A warning is shown if starting in `auto-edit` or `full-auto` without Git tracking.

*(Future Work: Granular network controls based on provider/command)*

### Platform sandboxing details

The sandboxing mechanism depends on your OS:

-   **macOS 12+:** Uses **Apple Seatbelt** (`sandbox-exec`). Read-only jail except for essential paths (`$PWD`, `$TMPDIR`, `~/.anything-codex`). Outbound network blocked by default for executed commands.
-   **Linux:** **Docker is recommended**. Anything Codex can run inside a minimal container, mounting your repo read/write. A custom firewall script can restrict egress (needs implementation/refinement for multi-provider). See `scripts/run_in_container.sh` (TODO: Adapt this script).

Both approaches aim for transparency in daily use.

---

## System Requirements

| Requirement                 | Details                                                         |
| --------------------------- | --------------------------------------------------------------- |
| Operating systems           | macOS 12+, Ubuntu 20.04+/Debian 10+, or Windows 11 **via WSL2** |
| Node.js                     | **22 or newer** (LTS recommended)                               |
| Git (optional, recommended) | 2.23+ for version control safety net                            |
| RAM                         | 4‑GB minimum (8‑GB recommended)                                 |
| (Optional) Docker           | For enhanced sandboxing on Linux                                |
| (Optional) Ollama           | For running local models                                        |

> Never run `sudo npm install -g`; fix npm permissions instead.

---

## CLI Reference

| Command                                      | Purpose                             | Example                                                        |
| -------------------------------------------- | ----------------------------------- | -------------------------------------------------------------- |
| `anything-codex`                             | Interactive REPL                    | `anything-codex`                                               |
| `anything-codex "…"`                         | Initial prompt for interactive REPL | `anything-codex "fix lint errors"`                             |
| `anything-codex -p <provider> -m <model> "…"` | Specify provider and model          | `anything-codex -p ollama -m llama3 "add type hints"`          |
| `anything-codex -q "…"`                      | Non‑interactive "quiet mode"        | `anything-codex -q --json "explain utils.ts"`                  |
| `anything-codex completion <bash\|zsh\|fish>` | Print shell completion script       | `anything-codex completion zsh`                                |

Key flags: `--provider/-p`, `--model/-m`, `--approval-mode/-a`, `--quiet/-q`. Use `anything-codex --help` for all options.

---

## Memory & Project Docs

Anything Codex can load contextual instructions from Markdown files:

1.  `~/.anything-codex/instructions.md` – Personal global guidance
2.  `anything-codex.md` at repo root – Shared project notes
3.  `anything-codex.md` in current working directory – Sub-package specifics

Disable with `--no-project-doc` or `ANYTHING_CODEX_DISABLE_PROJECT_DOC=1`.

---

## Non‑interactive / CI mode

Run Anything Codex headlessly in pipelines.

```yaml
# Example GitHub Action step
- name: Refactor code via Anything Codex (using Ollama)
  run: |
    npm install -g anything-codex
    # Configure Ollama endpoint if not default localhost
    # export OLLAMA_BASE_URL="http://ollama-service:11434"
    anything-codex -p ollama -m llama3 -a auto-edit --quiet "refactor services/legacy.py using modern patterns"
  env:
    # Pass secrets if using cloud providers
    # OPENAI_API_KEY: ${{ secrets.OPENAI_KEY }}
    # ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_KEY }}
```

Set `ANYTHING_CODEX_QUIET_MODE=1` to silence interactive UI elements.

---

## Recipes

Here are examples to get you started. Replace the prompt and adjust `--provider`/`--model` as needed.

| ✨  | What you type                                                                                       | What might happen (depending on LLM)                                       |
| --- | --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| 1   | `anything-codex -p openai -m gpt-4o "Refactor the Dashboard component to React Hooks"`              | Rewrites the component, runs `npm test`, shows diff.                       |
| 2   | `anything-codex -p ollama -m codellama "Generate SQL migration for adding 'last_login' to users"`   | Infers ORM, creates migration file, shows command to run it.               |
| 3   | `anything-codex -p anthropic -m claude-3-sonnet... "Write unit tests for utils/date.ts"`            | Generates tests using your test framework, runs them, iterates if needed.  |
| 4   | `anything-codex "Bulk-rename *.jpeg → *.jpg with git mv"`                                           | Generates and executes safe `git mv` commands.                             |
| 5   | `anything-codex "Explain what this regex does: ^(?=.*[A-Z]).{8,}$"`                                 | Outputs a step-by-step human explanation.                                  |
| 6   | `anything-codex -p google -m gemini-pro "Propose 3 high-impact, well-scoped PRs for this repo"`     | Analyzes code and suggests potential improvements.                         |
| 7   | `anything-codex --model llama3 "Review this script for potential security issues"`                  | Attempts to find and explain security bugs.                                |

---

## Installation

<details open>
<summary><strong>From npm (Recommended)</strong></summary>

```bash
npm install -g anything-codex
# or
yarn global add anything-codex
```

</details>

<details>
<summary><strong>Build from source</strong></summary>

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/anything-codex.git # Replace with actual repo URL
cd anything-codex

# Install dependencies and build
npm install
npm run build

# Get usage help
node ./dist/cli.js --help

# Run the locally-built CLI
node ./dist/cli.js "my prompt"

# Or link for global access during development
npm link
```

</details>

---

## Configuration

Manage providers and settings in `~/.anything-codex/config.yaml`. Environment variables override config file settings.

**Example `~/.anything-codex/config.yaml`:**

```yaml
# Default provider and model to use if not specified on the command line
defaultProvider: openai
defaultModel: o4-mini # Ensure this model exists for the default provider

# Approval mode for non-interactive runs if not specified
# Options: suggest, auto-edit, full-auto
defaultApprovalMode: suggest

# Behavior when 'full-auto' encounters an error
# Options: ask-user, ignore-and-continue, stop
fullAutoErrorMode: ask-user

# Define providers and their specific configurations
providers:
  openai:
    apiKey: sk-your-openai-key-here # Or use OPENAI_API_KEY env var
    # You can specify other OpenAI options here, like baseURL for proxies
    # model: gpt-4o # Default model for this provider if not specified

  ollama:
    # Base URL of your running Ollama instance
    baseURL: http://localhost:11434 # Or use OLLAMA_BASE_URL env var
    # model: llama3 # Default model for Ollama if not specified

  anthropic:
    apiKey: sk-ant-your-anthropic-key-here # Or use ANTHROPIC_API_KEY env var
    # model: claude-3-opus-20240229

  google:
    apiKey: your-google-api-key-here # Or use GOOGLE_API_KEY env var
    # model: gemini-1.5-pro-latest

  # Add other providers like openrouter, mistral, deepseek, azure...
  # openrouter:
  #   apiKey: sk-or-your-key-here
  #   # OpenRouter often needs model names prefixed, e.g., openai/gpt-4o
  #   model: mistralai/mistral-7b-instruct

# Custom instructions can also be defined here or in ~/.anything-codex/instructions.md
# instructions:
#   - Always provide code explanations.
#   - Prefer Python for scripting tasks unless specified otherwise.
```

---

## FAQ

<details>
<summary>How is this different from the original OpenAI Codex CLI?</summary>

The core difference is **flexibility**. Anything Codex is designed from the ground up to support multiple LLM backends (OpenAI, Anthropic, Google, local models via Ollama, etc.), whereas the original was tied to OpenAI's specific (now deprecated) Codex models and newer OpenAI models. We aim to maintain the powerful terminal integration and safety features while giving users choice.

</details>

<details>
<summary>How do I configure different LLM providers?</summary>

The recommended way is using the `~/.anything-codex/config.yaml` file, where you can list multiple providers and their API keys or base URLs. You can also use environment variables (e.g., `OPENAI_API_KEY`, `OLLAMA_BASE_URL`, `ANTHROPIC_API_KEY`). Use the `--provider` flag to select which one to use for a specific command.

</details>

<details>
<summary>How do I use local models like Llama 3?</summary>

1.  Install and run [Ollama](https://ollama.ai/).
2.  Pull the model you want: `ollama pull llama3`.
3.  Configure Anything Codex to point to your Ollama instance (usually `http://localhost:11434`) using the `config.yaml` file or the `OLLAMA_BASE_URL` environment variable.
4.  Run commands using `--provider ollama --model llama3`.

</details>

<details>
<summary>How do I stop Anything Codex from modifying my files?</summary>

By default (`--approval-mode suggest`), Anything Codex will **always ask for confirmation** before writing any file changes or executing any shell commands. You can review the proposed changes (diffs) or commands and answer **n** (no) to reject them. Only use `auto-edit` or `full-auto` if you understand the risks and ideally have your code under version control (Git).

</details>

<details>
<summary>Does it work on Windows?</summary>

Native Windows is not directly supported. You need to use [Windows Subsystem for Linux (WSL2)](https://learn.microsoft.com/en-us/windows/wsl/install). Testing has primarily been on macOS and Linux.

</details>

---

<!-- Section removed: Funding Opportunity (was specific to OpenAI) -->
<!-- Consider adding a "Sponsorship" section if applicable later -->

## Contributing

### Call for Contributors!

**Anything Codex needs your help!** This is a community effort to build a versatile and powerful tool. We especially need help with:

-   **Implementing New Provider Integrations:** Adding support for Anthropic, Google Gemini, Mistral, OpenRouter, Azure, etc. is a top priority.
-   **Improving Sandboxing:** Enhancing the security and cross-platform compatibility of command execution.
-   **Refining the Core Logic:** Improving prompt engineering, state management, and the interaction loop.
-   **Testing & Bug Fixing:** Ensuring reliability across different setups and providers.
-   **Documentation:** Making it easy for users and new contributors to get started.

Whether you're adding a new provider, fixing a bug, or improving the docs, your contribution is valuable!

### Development workflow

(Adapted from original, assuming similar tooling)

-   Fork the repository and create a *topic branch* from `main` (e.g., `feat/add-anthropic-provider`).
-   Keep changes focused. One provider or feature per PR.
-   Use `npm run test:watch` for fast feedback during development.
-   We use **Vitest** (or similar like Jest) for tests, **ESLint** + **Prettier** for style, and **TypeScript**.
-   Run checks before pushing: `npm test && npm run lint && npm run typecheck`
-   Sign the [Contributor License Agreement (CLA)](#contributor-license-agreement-cla) by adding the required comment to your PR.

```bash
# Watch mode for tests
npm run test:watch

# Type-check
npm run typecheck

# Fix lint/format issues
npm run lint:fix
npm run format:fix
```

### Adding New Providers

1.  **Create an Issue:** Discuss the provider integration first.
2.  **Add Configuration:** Update `config.yaml` structure and documentation (`README.md`, example config) to include the new provider's settings (API key, base URL, etc.).
3.  **Implement Client:** Create a new client module (e.g., `src/providers/anthropic.ts`) that handles API requests specific to that provider, conforming to a common interface (e.g., `LLMProvider`).
4.  **Integrate:** Update the main logic to recognize and use the new provider based on configuration/flags.
5.  **Add Tests:** Write unit/integration tests for the new provider client. Mock API calls.
6.  **Update Docs:** Add the provider to the [Supported Providers](#supported-providers) list and explain its configuration.

### Writing high‑impact code changes

1.  **Start with an issue:** Discuss the proposed change first.
2.  **Add/update tests:** Ensure test coverage for new features/fixes.
3.  **Document:** Update README, help text, or examples if user-facing behavior changes.
4.  **Atomic commits:** Keep commits logical and passing tests.

### Opening a pull request

-   Fill in the PR template (What? Why? How?).
-   Ensure local checks pass (`npm test && npm run lint && npm run typecheck`).
-   Keep your branch updated with `main`.
-   Mark PR as **Ready for review** when complete.

### Review process

1.  Maintainer(s) will review.
2.  Changes may be requested – this is part of the collaborative process!
3.  Once approved, the PR will be merged (likely squash-merged).

### Community values

-   **Be kind and inclusive:** Follow the [Contributor Covenant](https://www.contributor-covenant.org/).
-   **Assume good intent.**
-   **Teach & learn:** Help improve the project and documentation.

### Getting help

Use GitHub Discussions or Issues for questions, ideas, or help with setup. We're excited to build this together!

:rocket: **Happy Hacking!** :rocket:

### Contributor License Agreement (CLA)

*(Keep the CLA process as described in the original, ensuring it points to the correct CLA document for this new project if applicable, or adopt DCO)*

All contributors **must** accept the CLA / DCO.

**CLA Process:**
1. Open your PR.
2. Paste the following comment: `I have read the CLA Document and I hereby sign the CLA`
3. A bot will verify.

**DCO Process (Alternative):**
Ensure every commit has a `Signed-off-by: Your Name <your.email@example.com>` footer. Use `git commit -s` to add it automatically.

*(Maintainers: Decide whether to use CLA or DCO and update this section accordingly)*

#### Quick fixes (for DCO)

| Scenario          | Command                                          |
| ----------------- | ------------------------------------------------ |
| Amend last commit | `git commit --amend -s --no-edit && git push -f` |
| Multiple commits  | `git rebase -i HEAD~N` (replace N), add `-s`     |

### Releasing `anything-codex`

*(Adapt the release process scripts and commands for the new package name)*

To publish a new version:
1.  Go to the project root.
2.  Checkout a release branch: `git checkout -b release/vX.Y.Z`
3.  Update version numbers (e.g., in `package.json` and any internal version constants): `npm version patch|minor|major` (or manually edit).
4.  Commit the version bump (with DCO sign-off if using DCO): `git commit -am "chore(release): vX.Y.Z"` (use `-s` if needed).
5.  Build and publish: `npm run build && npm publish`
6.  Push the tag and branch: `git push origin vX.Y.Z && git push origin HEAD`
7.  Create a GitHub Release from the tag.

*(This process needs refinement based on the actual build/release scripts setup)*

---

## Security & Responsible AI

Found a vulnerability or have concerns about model usage/safety? Please report it responsibly.
-   **Security:** Open a confidential security advisory on GitHub or email [anything-codex/issues](https://github.com/8ankur8/anything-codex/issues) .
-   **Responsible AI/Safety:** Open a GitHub issue with the tag `responsible-ai`.

---

## License

This repository is licensed under the [Apache-2.0 License](LICENSE).


See [CONTRIBUTING.md](./CONTRIBUTING.md) for details.

## License

Licensed under the Apache-2.0 License.
