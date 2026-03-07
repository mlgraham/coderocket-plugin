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
  1. Sign up at https://deploy.coderocket.com (GitHub login)
  2. Install the CodeRocket Deploy GitHub App on your repos
  3. Go to Settings > API Keys and create a key (starts with `crk_`)
  4. Set `CODEROCKET_API_KEY` when Claude Code prompts for it, or export it in your shell
- If the health check fails, show troubleshooting steps
- Highlight any usage approaching limits
