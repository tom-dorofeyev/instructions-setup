# Copilot Instructions Setup

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub Copilot](https://img.shields.io/badge/Assistant-GitHub%20Copilot-000000?logo=githubcopilot)](https://github.com/features/copilot)
[![VS Code](https://img.shields.io/badge/Editor-VS%20Code-007ACC?logo=visualstudiocode&logoColor=white)](https://code.visualstudio.com/)

A reusable, production-ready instruction pack for GitHub Copilot Chat in VS Code.

This repository provides a clean baseline for teams that want consistent AI-assisted development standards across projects: coding style, clean code principles, SOLID, testing expectations, role-based agents, and reusable prompts.

## What’s Included

- **Global Copilot behavior** via `.github/copilot-instructions.md`
- **Reusable instruction modules** in `.github/instructions/`
- **Role-based agents** in `.github/agents/`
- **Prompt template** for pull request descriptions in `.github/prompts/`

## Repository Structure

```text
.github/
  copilot-instructions.md
  agents/
    software-engineer.md
    reviewer.md
    product-specialist.md
  instructions/
    clean-code-principles.instructions.md
    code-comments.instructions.md
    code-guidelines.instructions.md
    solid-principles.instructions.md
    testing-guidelines.instructions.md
  prompts/
    pr-description.prompt.md
```

## Quick Start

### 1) Add to an existing repository

From your target repository root:

```bash
mkdir -p .github
cp -R /path/to/this-repo/.github/* .github/
```

### 2) Commit and push

```bash
git add .github
git commit -m "Add reusable Copilot instructions setup"
git push
```

### 3) Start using Copilot Chat

Ask Copilot to implement, review, or spec a task. The instruction files and agents are picked up automatically from `.github/`.

## How It Works

### `copilot-instructions.md`
Defines global behavior and communication style used for general interactions.

### `instructions/*.instructions.md`
Adds rule sets that can be scoped with `applyTo` patterns (for example, test-only standards for `*.spec.*` and `*.test.*`).

### `agents/*.md`
Defines specialized personas for focused workflows:

- **Software Engineer** — implementation and delivery discipline
- **Reviewer** — systematic quality and correctness review
- **Product Specialist** — requirements/spec decomposition

### `prompts/*.prompt.md`
Reusable prompt templates that help standardize repetitive tasks like PR descriptions.

## Customization Guide

1. Update tone/style in `.github/copilot-instructions.md`
2. Add or refine standards in `.github/instructions/*.instructions.md`
3. Create team-specific agents in `.github/agents/`
4. Add workflow prompts in `.github/prompts/`

Keep edits focused and explicit so behavior remains predictable.

## Suggested Team Workflow

- Keep this setup in a dedicated template repo
- Sync updates into product repos on a regular cadence
- Treat instruction changes like code changes (PR + review)
- Version notable instruction updates via tags/releases

## Compatibility

- **Editor:** Visual Studio Code
- **Assistant:** GitHub Copilot Chat
- **Repository layout:** expects instruction files under `.github/`

## Contributing

Contributions are welcome. Prefer small, focused PRs that:

- Improve clarity and reduce ambiguity
- Preserve clean-code and SOLID guidance consistency
- Avoid overlapping or contradictory rules

## License

Distributed under the MIT License. See `LICENSE` for details.
