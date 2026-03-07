# CodeRocket Deploy - Claude Code Plugin

Generate production-ready CI/CD workflows for any codebase, directly from Claude Code. CodeRocket analyzes your repo, builds a GitHub Actions pipeline tailored to your stack, and opens a PR — zero to deployed in 60 seconds.

## What it does

1. **Analyze** — Scans your repository to detect language, framework, and deployment target
2. **Generate** — AI builds a production-ready GitHub Actions workflow for your stack
3. **Deploy** — Creates a PR with the workflow YAML — merge it and you're live

No more copy-pasting workflow templates or debugging YAML indentation. CodeRocket handles the entire CI/CD setup.

## Supported stacks

- **Languages:** JavaScript/TypeScript, Python, Go, Rust, Java, Ruby, PHP, C#/.NET
- **Frameworks:** React, Next.js, Vue, Angular, Django, Flask, FastAPI, Express, Rails, Laravel, Spring Boot
- **Deploy targets:** AWS, GCP, Azure, Vercel, Netlify, Fly.io, Railway, Render, Docker/K8s

## Prerequisites

- **Node.js 18+** — required for the MCP server (runs via `npx`)
- **Claude Code** — the plugin runs inside Claude Code

## Quick Start

### 1. Sign up for CodeRocket Deploy
Visit [deploy.coderocket.com](https://deploy.coderocket.com) and sign in with GitHub.

### 2. Install the GitHub App
Install the [CodeRocket Deploy GitHub App](https://github.com/apps/coderocket-deploy) on your repositories.

### 3. Generate an API key
Go to **Settings > API Keys** at [deploy.coderocket.com/settings](https://deploy.coderocket.com/settings) and create a new key. Keys start with `crk_`.

### 4. Set your API key

```bash
export CODEROCKET_API_KEY=crk_your_key_here
```

Add this to your shell profile (`~/.zshrc` or `~/.bashrc`) to persist across sessions.

### 5. Install the plugin

In Claude Code, run these commands:

```
/plugin marketplace add mlgraham/coderocket-plugin
/plugin install coderocket@coderocket-marketplace
```

Or from the CLI:

```bash
claude plugin marketplace add mlgraham/coderocket-plugin
claude plugin install coderocket@coderocket-marketplace
```

### 6. Deploy!

```
/coderocket:deploy
```

That's it. CodeRocket analyzes your repo, generates a workflow, and creates a PR.

## Commands

| Command | Description |
|---------|-------------|
| `/coderocket:deploy` | Generate a CI/CD workflow and create a PR |
| `/coderocket:status` | Check account status, usage, and connected repos |

## Pricing

- **Free:** 100 generations/month, 3 repos
- **Pro ($29/mo):** 1,000 generations/month, 20 repos
- **Team ($99/mo):** 5,000 generations/month, unlimited repos

Visit [deploy.coderocket.com](https://deploy.coderocket.com) for details.

## Troubleshooting

### MCP server won't connect
- Verify `CODEROCKET_API_KEY` is set in your environment: `echo $CODEROCKET_API_KEY`
- Check the key starts with `crk_` and hasn't been revoked
- Make sure Node.js 18+ is installed: `node --version`
- Run `/coderocket:status` to test connectivity

### Commands not showing up
- Run `/reload-plugins` to reload all plugins
- Or restart Claude Code to pick up plugin changes

### Plugin update
```bash
claude plugin marketplace update coderocket-marketplace
claude plugin update coderocket@coderocket-marketplace
```

### Plugin errors
- Open `/plugin` and check the **Errors** tab for details
- Run `claude --debug` for verbose plugin loading output

## Support

- [GitHub Issues](https://github.com/mlgraham/coderocket-plugin/issues)
- Email: support@coderocket.com
