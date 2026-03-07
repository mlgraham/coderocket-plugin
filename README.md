# CodeRocket Deploy - Claude Code Plugin

AI-powered deployment automation for Claude Code. Generate CI/CD workflows, create GitHub PRs, and review code — all from your terminal.

## Quick Start

### 1. Sign up for CodeRocket Deploy
Visit [deploy.coderocket.com](https://deploy.coderocket.com) and sign in with GitHub.

### 2. Install the GitHub App
Install the [CodeRocket Deploy GitHub App](https://github.com/apps/coderocket-deploy) on your repositories.

### 3. Generate an API key
Go to **Settings > API Keys** and create a new key.

### 4. Install the plugin

In Claude Code, run:
```
/plugin marketplace add mlgraham/coderocket-plugin
/plugin install coderocket@coderocket-marketplace
```

### 5. Deploy!
```
/coderocket:deploy
```

## Commands

| Command | Description |
|---------|-------------|
| `/coderocket:deploy` | Generate a CI/CD workflow and create a PR |
| `/coderocket:review` | View AI code reviews for your PRs |
| `/coderocket:status` | Check account status and connected repos |

## How it works

1. **Analyze** - CodeRocket scans your repository to detect language, framework, and deployment target
2. **Generate** - AI generates a production-ready GitHub Actions workflow
3. **Review** - You review the generated YAML and explanation
4. **Deploy** - Create a PR with one command, merge, and you're live

## Supported stacks

- **Languages:** JavaScript/TypeScript, Python, Go, Rust, Java, Ruby, PHP, C#/.NET
- **Frameworks:** React, Next.js, Vue, Angular, Django, Flask, FastAPI, Express, Rails, Laravel, Spring Boot
- **Deploy targets:** AWS, GCP, Azure, Vercel, Netlify, Fly.io, Railway, Render, Docker/K8s

## Pricing

- **Free:** 100 generations/month, 3 repos
- **Pro ($19/mo):** 1,000 generations/month, 20 repos
- **Team ($49/mo):** 5,000 generations/month, unlimited repos

Visit [coderocket.com/pricing](https://coderocket.com/pricing) for details.

## Support

- [Documentation](https://deploy.coderocket.com/docs)
- [GitHub Issues](https://github.com/mlgraham/coderocket-plugin/issues)
- Email: support@coderocket.com
