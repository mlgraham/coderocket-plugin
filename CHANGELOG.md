# Changelog

All notable changes to the **CodeRocket plugin** — for Claude Code and, as of 1.2.0,
Cursor — are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this
project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Two artifacts, two version numbers

This repository ships the **plugin**. The MCP server it talks to,
[`@mlgraham/coderocket-mcp`](https://www.npmjs.com/package/@mlgraham/coderocket-mcp),
is a separate npm package with its own version, pinned exactly in `.mcp.json` (Claude
Code) and `mcp.json` (Cursor).
They are versioned independently and the numbers are not expected to match — a
plugin release may pin an unchanged server, and a server patch may ship without a
plugin release. Every plugin entry below records which server version it pinned.

## Release checklist

The plugin's cache directory is keyed on the `version` field in
`.claude-plugin/plugin.json`. If that value does not change, a user who already has
the plugin installed can keep loading the previously cached copy. **Every published
change must bump the version**, or it may not reach users.

1. Make the change.
2. Bump `version` in **both** `.claude-plugin/plugin.json` and `.cursor-plugin/plugin.json`
   — the tooling in step 4 checks only the Claude one.
3. If the MCP server version changed, update the pin in **both** `.mcp.json` (Claude,
   flat, `${VAR:-}`) and `mcp.json` (Cursor, wrapped, `${VAR}`).
4. Add an entry here under a new heading.
5. Commit, then tag the release with Claude Code's own tooling:

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

## [1.2.0] — 2026-07-28

Adds a Cursor manifest alongside the Claude Code one, and the LICENSE file the
manifests have always claimed. Pins `@mlgraham/coderocket-mcp@1.0.2`.

### Changed

- **Pinned the MCP server to `@mlgraham/coderocket-mcp@1.0.2`** (was `1.0.1`), in both
  `.mcp.json` and Cursor's `mcp.json`. 1.0.2 is a security release and is the first
  version published through npm Trusted Publishing, so it carries a SLSA provenance
  attestation — the published tarball is cryptographically traceable to the workflow run
  and commit that built it. It contains:
  - **API URL validation.** `CODEROCKET_API_URL` was previously unvalidated, so anything
    able to set an environment variable in the Claude Code process — a cloned repo's
    `.envrc`, a devcontainer, CI config — could silently redirect a user's `crk_` key to
    an arbitrary host. Now an exact-hostname allowlist, HTTPS required off-loopback,
    embedded credentials rejected, and it fails closed rather than booting and leaking.
  - **A 30s request timeout**, so one hung response can no longer wedge the server.
  - **An exact dependency pin plus a regenerated lockfile**, clearing 8 npm audit
    findings (5 high) that the committed lockfile had been reinstalling.

### Added

- **`LICENSE`** (MIT, © 2026 CodeRocket, LLC). Both manifests declared `"license": "MIT"`
  while the repository granted no license at all — so a repo that is the live install
  path for a listed plugin was asserting a license it did not actually give. Cursor
  additionally requires a license file to publish.
- **`.cursor-plugin/plugin.json`** — Cursor's manifest. Carries `displayName` and a
  `variables` schema so Cursor can prompt for `CODEROCKET_API_KEY` at install time.
- **`mcp.json`** — Cursor's MCP config, in its wrapped `{"mcpServers": {...}}` form.

### Notes

- **`displayName` is deliberately confined to `.cursor-plugin/`.** It is valid in Cursor
  and is rejected by the Claude Code plugin validator — it was one of the keys removed in
  `3a5ea90` while fixing a failing install. It must never be added to
  `.claude-plugin/plugin.json`.
- **The two MCP configs coexist safely.** Claude Code discovers only the dotted
  `.mcp.json`; a plain `mcp.json` is ignored by it. Verified by installing this tree and
  confirming the component inventory still reports exactly one MCP server, rather than
  inferring it from the file names.
- The Cursor config uses `${CODEROCKET_API_KEY}` with no default, which is correct for
  Cursor (its `variables` schema makes the value required and prompts for it). Claude
  Code's `.mcp.json` keeps `${CODEROCKET_API_KEY:-}`, because there a bare `${VAR}` is
  treated as mandatory and its absence stops the MCP server from starting at all.

### Two new drift surfaces this release creates

Both are now part of the release checklist above:

1. **Two manifests, two version fields.** `.claude-plugin/plugin.json` and
   `.cursor-plugin/plugin.json` must be bumped together. `claude plugin tag` validates
   the Claude manifest against the marketplace entry — **it does not know
   `.cursor-plugin/` exists** and will not catch a stale Cursor version.
2. **Two MCP pins.** `.mcp.json` and `mcp.json` both pin the server version and must be
   bumped together when a new `@mlgraham/coderocket-mcp` is published.

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
  They still registered without it — what they lacked was discovery text: the model had
  little more than a bare name to match a user's request against, and the commands
  displayed no description. Measured across the two trees, the always-on descriptive
  budget went from ~74 to ~285 tokens, and `cicd-generation` itself from ~20 to ~190.
  (An earlier draft of this entry said the skill "could not be surfaced at all". That
  overstated it — corrected in 1.2.0 after installing both trees side by side.)
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

[1.2.0]: https://github.com/mlgraham/coderocket-plugin/releases/tag/coderocket--v1.2.0
[1.1.0]: https://github.com/mlgraham/coderocket-plugin/releases/tag/coderocket--v1.1.0
[1.0.1]: https://github.com/mlgraham/coderocket-plugin/compare/f3d62e1...f70fb4b
[1.0.0]: https://github.com/mlgraham/coderocket-plugin/compare/30cafd7...f3d62e1
