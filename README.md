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

## Quick Start

### 1. Sign up for CodeRocket Deploy
Visit [deploy.coderocket.com](https://deploy.coderocket.com) and sign in with GitHub.

### 2. Install the GitHub App
Install the [CodeRocket Deploy GitHub App](https://github.com/apps/coderocket-deploy) on your repositories.

### 3. Generate an API key
Go to **Settings > API Keys** at [deploy.coderocket.com/settings](https://deploy.coderocket.com/settings) and create a new key. Keys start with `crk_`.

### 4. Install the plugin

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

Claude Code will prompt you to enter your `CODEROCKET_API_KEY` when the MCP server connects. Paste the `crk_` key you created in step 3.

### 5. Deploy!

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
- **Pro ($19/mo):** 1,000 generations/month, 20 repos
- **Team ($49/mo):** 5,000 generations/month, unlimited repos

Visit [deploy.coderocket.com](https://deploy.coderocket.com) for details.

## Troubleshooting

### MCP server won't connect
- Verify your API key is set: Claude Code should prompt for `CODEROCKET_API_KEY` on first use
- Check the key starts with `crk_` and hasn't been revoked
- Run `/coderocket:status` to test connectivity

### Commands not showing up
- Run `/reload-plugins` to reload all plugins
- Or restart Claude Code to pick up plugin changes

### Plugin errors
- Open `/plugin` and check the **Errors** tab for details
- Run `claude --debug` for verbose plugin loading output

## Support

- [GitHub Issues](https://github.com/mlgraham/coderocket-plugin/issues)
- Email: support@coderocket.com
