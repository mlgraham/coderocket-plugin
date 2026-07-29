# Changelog

All notable changes to the **CodeRocket plugin for Claude Code** are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this
project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Two artifacts, two version numbers

This repository ships the **plugin**. The MCP server it talks to,
[`@mlgraham/coderocket-mcp`](https://www.npmjs.com/package/@mlgraham/coderocket-mcp),
is a separate npm package with its own version, pinned exactly in `.mcp.json`.
They are versioned independently and the numbers are not expected to match — a
plugin release may pin an unchanged server, and a server patch may ship without a
plugin release. Every plugin entry below records which server version it pinned.

## Release checklist

The plugin's cache directory is keyed on the `version` field in
`.claude-plugin/plugin.json`. If that value does not change, a user who already has
the plugin installed can keep loading the previously cached copy. **Every published
change must bump the version**, or it may not reach users.

1. Make the change.
2. Bump `version` in `.claude-plugin/plugin.json`.
3. Add an entry here under a new heading.
4. Commit, then tag the release with Claude Code's own tooling:

   ```bash
   claude plugin validate .          # manifests must pass
   claude plugin tag --dry-run .     # confirm what will be tagged
   claude plugin tag .               # creates coderocket--v<version>
   ```

   `claude plugin tag` creates a `{name}--v{version}` tag and refuses to run if
   `plugin.json` and the marketplace entry disagree, or if the working tree is dirty —
   so the tag, the manifest version, and this file cannot drift apart. Push the tag
   separately (`--push`), once the release is meant to be public.

---

## [1.1.0] — 2026-07-28

First release to declare a version. Pins `@mlgraham/coderocket-mcp@1.0.1` (unchanged).

### Added

- **`version` field in `.claude-plugin/plugin.json`.** Claude Code names the plugin's
  cache directory after this value; with no version field it falls back to the git
  commit sha (or literally `unknown`). Declaring a version makes installs
  reproducible and inspectable — verified against the current Claude Code plugin
  cache, where a versioned plugin resolves to `cache/<marketplace>/<plugin>/<version>/`
  and can hold several versions side by side.
- **This changelog**, plus the release checklist above.
- **YAML frontmatter on all three commands and on the skill.** None of them had any.
  A skill with no frontmatter `description` carries no discovery metadata, so
  `cicd-generation` could not be surfaced the way its own text claimed it would be, and
  the three commands displayed no description to the user.
- **`/coderocket:review` is now documented.** The command has existed since the
  initial release but appeared in no user-facing documentation, so code review was
  effectively invisible to anyone installing from the marketplace.

### Changed

- **README rewritten.** Correct install order (API key before install, because the
  MCP server reads `CODEROCKET_API_KEY` from the environment at startup), all three
  slash commands documented, a worked example of a real `/coderocket:deploy` run, and
  an expanded troubleshooting section covering the "tools return not configured"
  case.

### Fixed

- **Documented that Claude Code never prompts for an API key.** The server reads
  `process.env.CODEROCKET_API_KEY` once at startup and starts *successfully* with an
  empty key, so a user who waits for a prompt waits forever and every tool reports
  that the key is not set. The README now states this plainly and gives the shell
  profile flow instead.

### Reversed

- **Restores the `version` field removed in `fb1eba6` (2026-03-06).** That removal
  was correct at the time: the version string was recorded in the install path while
  the cache directory was created from the commit sha, and the mismatch stopped the
  plugin from loading. That behaviour no longer reproduces — the path and the
  recorded version are now derived consistently from the same value.

---

## [1.0.1] — 2026-03-07

Never declared in `plugin.json`; reconstructed from git history for the record.
Pinned `@mlgraham/coderocket-mcp@1.0.1`.

### Fixed

- Switched the MCP server from HTTP back to stdio transport (`18cc647`). HTTP
  transport in Claude Code always engages an OAuth provider and, with no OAuth
  implementation on the backend, the connection died with zero tools loaded. stdio
  does not trigger OAuth.
- Gave `CODEROCKET_API_KEY` a default (`${CODEROCKET_API_KEY:-}`) so a missing
  environment variable no longer prevents the MCP server from starting at all
  (`5137d52`).
- Corrected pricing to match production — $29/mo Pro, $99/mo Team (`e9b48fb`).
- Removed the duplicate inline `mcpServers` block from `plugin.json`, leaving
  `.mcp.json` as the single source of MCP configuration (`940657c`, `3a5ea90`).
- Removed keys rejected by the Claude Code plugin validator, and pinned the npm
  package to an exact version instead of a floating tag (`fb1eba6`).

### Changed

- Rewrote the plugin pitch to lead with deployment (`3da5e62`).

## [1.0.0] — 2026-03-02

Initial release. Analyze a repository, generate a GitHub Actions workflow, open a PR.

[1.1.0]: https://github.com/mlgraham/coderocket-plugin/releases/tag/coderocket--v1.1.0
[1.0.1]: https://github.com/mlgraham/coderocket-plugin/compare/f3d62e1...f70fb4b
[1.0.0]: https://github.com/mlgraham/coderocket-plugin/compare/30cafd7...f3d62e1
