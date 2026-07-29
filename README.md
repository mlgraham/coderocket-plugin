# CodeRocket for Claude Code

Generate production-ready CI/CD workflows without leaving Claude Code. CodeRocket reads
your repository, works out how it should be built and shipped, writes the GitHub Actions
pipeline, and opens a pull request — typically in under a minute. It then reviews the pull
requests that follow.

```
/coderocket:deploy
```

---

## What it does

| | |
|---|---|
| **Analyze** | Scans the repository and detects language, framework, and deploy target |
| **Generate** | Writes a GitHub Actions workflow for that specific stack — no template copy-paste |
| **Open a PR** | Delivers the workflow as a pull request with a plain-English explanation, so you review before anything runs |
| **Review** | Reads subsequent pull requests and posts inline findings by severity |

Nothing is pushed to your default branch. Every change arrives as a pull request you can
edit or close.

## Supported stacks

- **Languages** — JavaScript/TypeScript, Python, Go, Rust, Java, Ruby, PHP, C#/.NET
- **Frameworks** — React, Next.js, Vue, Angular, Django, Flask, FastAPI, Express, Rails, Laravel, Spring Boot
- **Deploy targets** — AWS, GCP, Azure, Vercel, Netlify, Fly.io, Railway, Render, Docker/K8s

## Requirements

- **Claude Code** — the plugin runs inside it
- **Node.js 18+** — the MCP server runs via `npx`
- A free CodeRocket account at [deploy.coderocket.com](https://deploy.coderocket.com)

---

## Setup

> **Do the API key step before installing.** The MCP server reads `CODEROCKET_API_KEY`
> from your environment **once, at startup**. Claude Code will **never prompt you for
> it** — if the variable is missing the server still starts, and every CodeRocket tool
> simply reports that no key is configured.

### 1. Create an account and connect GitHub

Sign in at [deploy.coderocket.com](https://deploy.coderocket.com) with GitHub, then install
the [CodeRocket GitHub App](https://github.com/apps/coderocket-deploy) on the repositories
you want it to work with. Private repositories are supported; permissions are minimal and
revocable.

### 2. Generate an API key

**Settings → API Keys** at
[deploy.coderocket.com/settings](https://deploy.coderocket.com/settings). Keys begin with
`crk_`. Copy it now — it is shown once.

### 3. Put the key in your shell profile

```bash
echo 'export CODEROCKET_API_KEY=crk_your_key_here' >> ~/.zshrc   # or ~/.bashrc
source ~/.zshrc
```

Confirm it is set, in the same shell you will launch Claude Code from:

```bash
echo $CODEROCKET_API_KEY
```

### 4. Install the plugin

In Claude Code:

```
/plugin marketplace add mlgraham/coderocket-plugin
/plugin install coderocket@coderocket-marketplace
```

Or from your terminal:

```bash
claude plugin marketplace add mlgraham/coderocket-plugin
claude plugin install coderocket@coderocket-marketplace
```

### 5. Restart Claude Code

Required. A session that was already running started the MCP server with the old
environment and will not pick up a newly exported key.

### 6. Check it works

```
/coderocket:status
```

A healthy result reports API connectivity, your account tier, and usage this month.

---

## Commands

### `/coderocket:deploy`

Generate a CI/CD workflow for the current project and open a pull request.

Checks connectivity, finds the repository, analyzes the stack, shows you what it detected
and what the pipeline will do, and opens the PR once you approve. If the repository is not
connected yet, it tells you what to install.

### `/coderocket:review`

View AI code reviews for your pull requests.

Lists recent reviews with PR title, repository, severity counts, and status, and drills
into a single review to show the individual findings and the lines they apply to. Reviews
are produced automatically for pull requests on connected repositories — this command reads
them; it does not trigger them.

### `/coderocket:status`

Account and system dashboard: API health, account tier, usage against your monthly
allowance, connected repositories, and recent activity. Run this first whenever something
looks wrong.

---

## Example

```
> /coderocket:deploy

  Checking CodeRocket connectivity...            ok
  Repository: acme/storefront                    connected

  Detected
    Language    Python 3.12
    Framework   Django 5
    Services    PostgreSQL
    Target      AWS ECS (Fargate)

  Proposed pipeline: .github/workflows/deploy.yml
    test → build → migrate → deploy → smoke test

  Generate this workflow and open a pull request? (y/n) y

  Pull request #41 opened  ·  +118 −0
  https://github.com/acme/storefront/pull/41
```

Merge the PR and the next push to your default branch deploys.

---

## Pricing

| Plan | Generations / month | Code reviews / month | Repositories |
|---|---|---|---|
| **Free** | 100 | 50 | 3 |
| **Pro** — $29/mo | 1,000 | 500 | 20 |
| **Team** — $99/mo | 5,000 | Unlimited | Unlimited |

No credit card required for the free tier. See
[deploy.coderocket.com](https://deploy.coderocket.com) for current details.

---

## Troubleshooting

### Tools report that the API key is not configured

The most common setup problem, and the one that looks like nothing happening at all.
Claude Code does not prompt for the key — it must be in the environment **before** Claude
Code starts.

1. `echo $CODEROCKET_API_KEY` in the shell you launch Claude Code from. Empty means the
   export did not persist — check it is in `~/.zshrc` or `~/.bashrc`, not just typed into a
   one-off shell.
2. Confirm the key begins with `crk_`.
3. Restart Claude Code. The server reads the variable once at startup.

### "API key has been revoked"

The key was deliberately revoked from **Settings → API Keys**. It cannot be reinstated —
generate a new one, update your shell profile, and restart Claude Code. (This message is
distinct from "Invalid API key", which means the key does not exist, and from "API key has
expired".)

### MCP server won't connect

- `node --version` — must be 18 or newer
- Open `/plugin` and check the **Errors** tab
- `claude --debug` prints verbose plugin and MCP loading output

### Commands don't appear

- `/reload-plugins`, or restart Claude Code
- Confirm the install: `/plugin` should list **coderocket**

### Updating

```bash
claude plugin marketplace update coderocket-marketplace
claude plugin update coderocket@coderocket-marketplace
```

If an update does not seem to take effect, check the installed version in `/plugin` against
the latest entry in [CHANGELOG.md](CHANGELOG.md).

---

## Versioning

The plugin and the MCP server are separate artifacts with independent version numbers. The
plugin's version lives in `.claude-plugin/plugin.json` (and, for Cursor, in
`.cursor-plugin/plugin.json` — the two are bumped together); the server
([`@mlgraham/coderocket-mcp`](https://www.npmjs.com/package/@mlgraham/coderocket-mcp)) is
pinned to an exact version in `.mcp.json`. Changes to both are recorded in
[CHANGELOG.md](CHANGELOG.md), and each release is tagged `coderocket--v<version>` via
`claude plugin tag`.

### Editor support

Claude Code reads `.claude-plugin/plugin.json` and `.mcp.json`. Cursor reads
`.cursor-plugin/plugin.json` and `mcp.json`. The two sets sit side by side and neither
editor picks up the other's files — Claude Code discovers only the dotted `.mcp.json`.

Licensed MIT — see [LICENSE](LICENSE).

## Support

- [GitHub Issues](https://github.com/mlgraham/coderocket-plugin/issues)
- support@coderocket.com

MIT licensed.
