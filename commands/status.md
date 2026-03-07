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
- If the API key is not set, show setup instructions:
  1. Create a key at https://deploy.coderocket.com/settings
  2. Add `export CODEROCKET_API_KEY=crk_...` to `~/.zshrc` or `~/.bashrc`
  3. Restart Claude Code
- If the health check fails, show troubleshooting steps
- Highlight any usage approaching limits
