# xray-xhttp-relay-deploy

[![Release](https://img.shields.io/github/v/release/superchaospc/xray-xhttp-relay-deploy?sort=semver)](https://github.com/superchaospc/xray-xhttp-relay-deploy/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![Claude Code](https://img.shields.io/badge/Claude%20Code-skill-d97757)
![Codex](https://img.shields.io/badge/Codex-skill-412991)
![Tested](https://img.shields.io/badge/live--tested-16%2F16%20menus-brightgreen)
![Shell](https://img.shields.io/badge/shell-bash-4EAA25?logo=gnu-bash&logoColor=white)

**English** | [中文说明](#中文说明)

A **Claude Code / Codex skill** that installs and remotely operates the
[`xray-xhttp-relay`](https://github.com/superchaospc/xray-xhttp-relay) one-click
**VLESS + XHTTP + REALITY** relay script on a VPS over SSH. It drives the script's
16-item interactive menu **non-interactively** by feeding stdin over SSH — it does
**not** reimplement any relay logic.

This is the sibling of `xray-relay-deploy` (which drives the TCP+XTLS-Vision variant).
Same drive-the-menu mechanism; this one targets the **XHTTP** transport (requires Xray
core ≥ 24.10.31).

## What it does

Once installed, the skill lets the agent — on a VPS you name (SSH alias or host) —

- **deploy** a fresh XHTTP+REALITY relay (residential SOCKS5 multi-hop or VPS-direct)
- **manage nodes**: add / delete / rename, single or batch (menus 2, 3, 12–16)
- **operate**: status, traffic, diagnostics, change port, update/restart Xray, monitoring/email alerts, uninstall
- **retrieve** the generated VLESS links (`type=xhttp`/`path`/`mode`) and subscription

The mechanism: the script reads every prompt through `prompt_read`, which exits cleanly
on stdin EOF, so a fixed answer sequence drives any menu function. The exact sequence for
each menu item lives in [`references/menu-actions.md`](references/menu-actions.md).

> **Live-tested:** all 16 menus + the monitor sub-menu were driven end-to-end against a
> real XHTTP VPS; every stdin sequence checks out.

## Install (Claude Code + Codex)

```bash
# 1. Clone into Claude Code's skills dir
git clone https://github.com/superchaospc/xray-xhttp-relay-deploy \
  ~/.claude/skills/xray-xhttp-relay-deploy

# 2. To use it in Codex too, run the bundled installer once
~/.claude/skills/xray-xhttp-relay-deploy/install.sh
```

Claude Code and Codex share the same `SKILL.md` format — `install.sh` just symlinks the
skill into Codex's dirs (`~/.codex/skills`, `~/.agents/skills`). Self-contained, portable
(uninstalled agents skipped), idempotent. Restart Codex so it rescans.

The skill triggers on phrases like "装个 xhttp 中转", "xhttp reality 节点",
"部署 xhttp 落地/直连", "给 vps 加几个 xhttp 直连节点".

## Authorization & safety

This skill operates the user's **own** script on the user's **own** VPS — that's the
intended use. Destructive functions (delete / change-port / uninstall / fresh-install,
which overwrites a live config with no migration) are gated: the skill shows exactly what
it will run and on which target, and waits for confirmation before sending.

---

## 中文说明

一个 **Claude Code / Codex skill**,通过 SSH 在 VPS 上安装并远程操作
[`xray-xhttp-relay`](https://github.com/superchaospc/xray-xhttp-relay) 一键
**VLESS + XHTTP + REALITY** 中转脚本。它**非交互地**驱动脚本的 16 项交互菜单(通过 SSH 喂
stdin),**不重新实现**任何中转逻辑。

它是 `xray-relay-deploy`(驱动 TCP+XTLS-Vision 版)的姊妹 skill。驱动机制相同,这个针对
**XHTTP** 传输(需要 Xray core ≥ 24.10.31)。

### 能做什么

装上后,agent 可以在你指定的 VPS(SSH 别名或主机)上:

- **部署**全新 XHTTP+REALITY 中转(住宅 SOCKS5 多跳 或 VPS 直连)
- **管理节点**:单条/批量 添加、删除、改名(菜单 2、3、12–16)
- **运维**:状态、流量、诊断、改端口、更新/重启 Xray、监控邮件告警、卸载
- **取回**生成的 VLESS 链接(`type=xhttp`/`path`/`mode`)和订阅

原理:脚本每个 prompt 都经 `prompt_read` 读取,stdin 到 EOF 时干净退出,所以一串固定答案就能
驱动任意菜单功能。每个菜单项的精确序列见
[`references/menu-actions.md`](references/menu-actions.md)。

> **已实测:** 全部 16 项菜单 + 监控子菜单都在真实 XHTTP VPS 上端到端跑通,每条 stdin 序列都验证正确。

### 安装(Claude Code + Codex)

```bash
# 1. clone 到 Claude Code 的 skills 目录
git clone https://github.com/superchaospc/xray-xhttp-relay-deploy \
  ~/.claude/skills/xray-xhttp-relay-deploy

# 2. 想在 Codex 里也能用,再跑一次自带安装脚本
~/.claude/skills/xray-xhttp-relay-deploy/install.sh
```

CC 和 Codex 用同一种 `SKILL.md` 格式,`install.sh` 只是把 skill 软链进 Codex 的目录
(`~/.codex/skills`、`~/.agents/skills`)。自包含、可移植(没装的 agent 自动跳过)、幂等。
完成后重开 Codex 让它重新扫描。

触发词例如:"装个 xhttp 中转"、"xhttp reality 节点"、"部署 xhttp 落地/直连"、
"给 vps 加几个 xhttp 直连节点"。

### 授权与安全

本 skill 操作的是你**自己的**脚本、你**自己的** VPS——这正是它的预期用途。破坏性功能(删节点 /
改端口 / 卸载 / 全新安装——后者会覆盖 live 配置且无自动迁移)都有确认门槛:skill 会先展示将要
执行的命令和目标,经你确认后才发送。

## License

[MIT](LICENSE)
