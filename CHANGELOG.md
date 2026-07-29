# Changelog

All notable changes to the **CodeRocket plugin** for Claude Code are documented here.
Cursor packaging shipped on `main` in 1.2.0 and moved to the `cursor-marketplace` branch
in 1.2.2 — see that entry for why.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this
project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Two artifacts, two version numbers

This repository ships the **plugin**. The MCP server it talks to,
[`@mlgraham/coderocket-mcp`](https://www.npmjs.com/package/@mlgraham/coderocket-mcp),
is a separate npm package with its own version, pinned exactly in `.mcp.json` — the one
and only MCP server declaration on this branch.
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
3. If the MCP server version changed, update the pin in `.mcp.json` (flat, `${VAR:-}`).
   **Do not add a second MCP declaration to this branch** — see 1.2.2.
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

## [1.2.2] — 2026-07-29

Packaging correctness on the live distribution path. Removes the second MCP server
declaration and the Cursor manifest from `main`, so this branch presents exactly one
`coderocket` MCP server. Pins `@mlgraham/coderocket-mcp@1.0.2` (unchanged). No change to
command or skill behaviour.

### Why

The Anthropic community marketplace pins this repo by commit SHA. The automated bump PR
for 1.2.0 ([#1556](https://github.com/anthropics/claude-plugins-community/pull/1556)) passed
both automated checks and then sat unmerged while its immediate neighbours — PRs 1548–1555
and 1557 — were all merged by a maintainer within the same hour. A contiguous run of merges
with exactly one gap is a deliberate skip, not queue position or a batch cap, and this repo
is not on the marketplace's freeze list.

The likeliest objection is visible in the 1.2.0 diff: it added `mcp.json` **alongside** the
existing `.mcp.json`, so a single plugin declared a server named `coderocket` twice, each
copy carrying `CODEROCKET_API_KEY`. Two credential-bearing MCP declarations in one plugin is
a reasonable thing for a reviewer or a security scan to stop on, and duplicate registration
is a known Claude Code failure mode regardless of who is reviewing. The same diff also
introduced `.cursor-plugin/plugin.json`, a competing editor's manifest carrying a
`displayName` key that the Claude Code validator rejects.

Both are removed here rather than defended. This is a hypothesis about the skip, not a
confirmed cause — no reviewer feedback was given. If the next automated bump PR is skipped
again, the cause lies elsewhere and the escalation path is the
[plugin directory submission form](https://clau.de/plugin-directory-submission); pull
requests opened directly against the marketplace repo are closed automatically.

### Removed

- **`mcp.json`** (Cursor's wrapped `mcpServers` format). `.mcp.json` is now the only MCP
  server declaration on `main`.
- **`.cursor-plugin/plugin.json`**. `displayName` is valid in Cursor and **invalid in the
  Claude Code plugin manifest schema** — it was one of the keys removed in `3a5ea90` while
  fixing a failed install.

Both files are preserved on the
[`cursor-marketplace`](https://github.com/mlgraham/coderocket-plugin/tree/cursor-marketplace)
branch, cut from the 1.2.1 tree. Cursor's marketplace reads a submitted repository
reference, so a branch remains a viable source for that channel; nothing is lost.

### Changed

- README "Editor support" now states the single-server invariant and points at the branch.
- Release checklist no longer instructs bumping two manifests or two MCP pins, and now says
  explicitly not to add a second MCP declaration to `main`.

## [1.2.1] — 2026-07-29

Documentation correctness. Removes the last two places that told users to wait for an
API-key prompt that cannot arrive, fixes a verification step that can pass while setup is
broken, and adds CI so neither can come back. Pins `@mlgraham/coderocket-mcp@1.0.2`
(unchanged).

### Fixed

- **`commands/deploy.md` and `commands/status.md` no longer claim Claude Code will ask for
  the key.** 1.1.0 fixed this claim in the README and recorded it as fixed, but both slash
  commands still carried it — `deploy.md` as *"Claude Code will prompt you to enter the key
  when the plugin connects"*, `status.md` as *"Set `CODEROCKET_API_KEY` when Claude Code
  prompts for it"*. These are the instructions the model reads and repeats to the user
  during the exact failure they describe, so a user hitting an auth error was told to wait
  for a prompt that does not exist. Both now carry the setup text verbatim from the MCP
  server's own `handleHealthCheck()` — one wording, one source of truth — plus an explicit
  instruction to the model never to promise a prompt.

  This patch has now been scoped wrong twice, both times because the fix list came from a
  previous summary rather than from a grep of the tree. The guard below exists so a summary
  can never again be the thing that decides the patch is complete.

- **`README.md` verified the wrong thing.** Setup and troubleshooting both used
  `echo $CODEROCKET_API_KEY` to confirm the key was in place. `echo` prints a shell
  variable whether or not it was ever exported — so `CODEROCKET_API_KEY=crk_...` without
  `export` prints the key, looks like success, and hands the MCP server nothing. Both now
  use `printenv CODEROCKET_API_KEY`, which reads only the exported environment (the same
  thing Claude Code passes to the server) and exits non-zero when it is absent. The
  troubleshooting steps also state the ordering explicitly: export first, then launch
  Claude Code from that shell — exporting into a shell Claude Code is already running in
  does nothing until restart.

### Added

- **`.github/workflows/content-guard.yml`** — the repository's first CI workflow. Greps the
  tree on every push and pull request and fails if `will prompt you`, `Claude Code prompts`,
  `prompt you to enter`, or `echo $CODEROCKET_API_KEY` reappear. It runs over the whole tree
  — so files that do not exist yet are covered too — with two deliberate exemptions:
  `.github/workflows/`, or the guard matches its own pattern list, and this changelog, which
  has to quote the removed wording verbatim in order to record it. The patterns are written
  narrowly enough to leave the Cursor notes below alone: Cursor genuinely does prompt via
  its `variables` schema, and saying so is not the defect.

### Notes

- Documentation only — no change to the MCP server pin, the manifests' behaviour, or any
  tool. The version bump exists because the plugin cache is keyed on `version`: without it,
  an already-installed user keeps loading the cached 1.2.0 copy and never sees the fix.
  Bumped in both `.claude-plugin/plugin.json` and `.cursor-plugin/plugin.json`, per the
  release checklist above.

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

[1.2.1]: https://github.com/mlgraham/coderocket-plugin/releases/tag/coderocket--v1.2.1
[1.2.0]: https://github.com/mlgraham/coderocket-plugin/releases/tag/coderocket--v1.2.0
[1.1.0]: https://github.com/mlgraham/coderocket-plugin/releases/tag/coderocket--v1.1.0
[1.0.1]: https://github.com/mlgraham/coderocket-plugin/compare/f3d62e1...f70fb4b
[1.0.0]: https://github.com/mlgraham/coderocket-plugin/compare/30cafd7...f3d62e1
