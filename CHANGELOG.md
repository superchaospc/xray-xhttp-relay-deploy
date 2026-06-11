# Changelog

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] — 2026-06-11

### Added
- Initial public release. A **Claude Code / Codex skill** that installs and remotely
  operates the [`xray-xhttp-relay`](https://github.com/superchaospc/xray-xhttp-relay)
  one-click **VLESS + XHTTP + REALITY** relay script (requires Xray core ≥ 24.10.31) on a
  VPS over SSH, driving its 16-item interactive menu non-interactively (stdin-feed + EOF
  contract).
- **Live-tested 16/16:** all 16 menus + the monitor sub-menu were driven end-to-end
  against a real XHTTP VPS; every stdin sequence is confirmed.
- `SKILL.md` — when to trigger + the install/drive flow, including XHTTP knobs
  (`XHTTP_MODE`, `XHTTP_PATH`, `REALITY_SERVER_NAME`).
- `references/menu-actions.md` — exact stdin sequence for every menu item.
- `install.sh` — self-contained cross-tool installer (symlinks the skill into Codex's
  `~/.codex/skills` / `~/.agents/skills`).
- Bilingual (English / 中文) README, MIT LICENSE.

Sibling of [`xray-relay-deploy`](https://github.com/superchaospc/xray-relay-deploy)
(the TCP+XTLS-Vision variant); they share the identical 16-item menu.

[1.0.0]: https://github.com/superchaospc/xray-xhttp-relay-deploy/releases/tag/v1.0.0
