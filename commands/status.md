---
description: Check CodeRocket account status, API health, usage, and connected repos
---

# /coderocket:status

Check your CodeRocket account status, API health, and repository summary.

## Instructions

You are helping the user check their CodeRocket Deploy status. Run all checks and present a dashboard.

### Step 1: Health check
Call `health_check` to verify API connectivity.

### Step 2: Account status
Call `account_status` to get the user's account information.

### Step 3: Repository summary
Call `list_repos` to get an overview of connected repositories.

### Present the dashboard

Format the output as a status dashboard:

```
CodeRocket Deploy Status
========================

API: Connected
Account: username (Pro tier)

Usage This Month:
  Generations: 42 / 1000
  Reviews: 15 / 500
  Repos: 5 / 20

Repositories:
  myorg/frontend    - Analyzed (React, Vercel)
  myorg/backend     - Analyzed (Python, AWS ECS)
  myorg/api         - Pending analysis
```

## Notes
- If authentication fails, show setup instructions:
  1. Sign up at https://deploy.coderocket.com
  2. Install the GitHub App on your repositories
  3. Go to Settings > API Keys and create a key
  4. Add to your shell profile (~/.zshrc or ~/.bashrc): export CODEROCKET_API_KEY=crk_your_key_here
  5. Restart Claude Code (the MCP server reads the key from your shell environment on startup)

  Never tell the user that Claude Code will ask them for the key — it cannot. The MCP
  server reads the environment once at startup and starts successfully without a key, so
  a user waiting to be asked waits forever.
- If the health check fails, show troubleshooting steps
- Highlight any usage approaching limits
