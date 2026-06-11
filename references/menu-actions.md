# Menu actions — exact stdin sequences (XHTTP variant)

Every action below is driven with the universal contract from SKILL.md:

```bash
printf '<menu#>\n<answers...>\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

Each line in the `printf` is one answer to one prompt, in order. EOF after the last line exits the script cleanly. An **empty line** (`\n` with nothing before it) submits an empty answer, which most prompts treat as "use the default".

To set an XHTTP knob for the run, put the env var on the remote side: `... | ssh <target> 'XHTTP_MODE=packet-up bash /root/xray_deploy.sh'`. Menu numbers and answer order are identical to the Vision variant — only the transport differs.

`<IDX>` / node numbers come from the node list the script prints (run menu `5` first to see current nodes and their numbers). `🔴` marks destructive actions that require explicit user confirmation before sending (see SKILL.md).

## Node input formats

Residential SOCKS5 nodes accept two formats:
- `host:port:user:pass`  ← common, preferred
- `socks5://user:pass@host:port`  ← use when the password contains `:@/` etc.

Names (备注名称) are sanitized: spaces and `#?&` become `-`. Empty name → an auto default (`Node-N`, `VPS-Direct`, etc.).

Listening ports are assigned automatically by the script: the first node gets `443`, later ones get `8442 + index` (residential) or the next free port (direct/batch). You do not choose listen ports except via menu 4. Each node also gets its own random XHTTP path (override a single one with `XHTTP_PATH=...`).

---

## 1 — 全新安装 (fresh install)

Runs the full pipeline (install xray ≥ 24.10.31 → keys → collect nodes → XHTTP+REALITY config → firewall → start). The interactive part is **collect_nodes**, a loop:

For each residential SOCKS5 node, two lines: the node string, then its name (empty = `Node-N`). Finish with `done`.

```bash
# Two residential nodes then finish:
printf '1\n1.2.3.4:1080:alice:pw\nKR-Seoul\n5.6.7.8:1080:bob:pw\nUS-LA\ndone\n' \
  | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

If you finish with `done` having added **no** nodes, the script asks whether to create a 443 direct starter node (`输入 y ...`). Answer `y` then a name (empty = `VPS-Direct`):

```bash
# Direct-only starter node:
printf '1\ndone\ny\n\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

⚠️ A fresh install **replaces** any existing `/usr/local/etc/xray/config.json` with no migration. On a live box, confirm with the user and back up first.

After install, read `/root/xray_nodes_info.txt` for the VLESS links (they carry `type=xhttp`/`path`/`mode`).

## 2 — 添加节点 (add one residential SOCKS5 node)

Node string, then name. Requires an existing install.

```bash
printf '2\n1.2.3.4:1080:alice:pw\nKR-Seoul\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 3 — 删除节点 🔴

Node index to delete.

```bash
printf '3\n<IDX>\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 4 — 修改端口 🔴

Node index, then new port.

```bash
printf '4\n<IDX>\n<NEW_PORT>\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 5 — 查看状态 (read-only)

No answers needed. After printing status it asks which node's QR to show; just let EOF end it.

```bash
printf '5\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 6 — 流量统计 (read-only)

```bash
printf '6\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 7 — 排错诊断 (read-only)

No prompts; runs a full service/config/port/firewall/BBR/log diagnostic.

```bash
printf '7\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 8 — 更新 Xray (restarts service)

Confirmation `y/n`. Keeps Xray ≥ 24.10.31.

```bash
printf '8\ny\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 9 — 重启 Xray (restarts service)

```bash
printf '9\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 10 — 监控报警 (monitor sub-menu)

After `10`, choose a sub-letter:
- `a` 配置邮件 → then 5 lines: SMTP host, SMTP port (587/465), from-email, password/app-token, to-email
- `b` 启动监控
- `c` 停止监控 🔴
- `d` 查看监控日志 (read-only)
- `e` 发送测试邮件
- `f` 返回主菜单

```bash
# Configure email alerts:
printf '10\na\nsmtp.gmail.com\n587\nme@gmail.com\nAPP_TOKEN\nme@gmail.com\n' \
  | ssh <target> 'bash /root/xray_deploy.sh' 2>&1

# Start monitoring:
printf '10\nb\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1

# Send a test email:
printf '10\ne\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 11 — 卸载 🔴 (removes Xray entirely)

Confirm `y/n`, then it asks whether to also remove `/swapfile` (`y/n`).

```bash
printf '11\ny\nn\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 12 — 添加 VPS 直连节点

One line: name (empty = `VPS-Direct`).

```bash
printf '12\nJP-Direct\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 13 — 批量添加住宅 SOCKS5 节点 (up to 20)

One node per line (`host:port:user:pass` or `socks5://...`), names auto-derived from host/IP, finish with `done`.

```bash
printf '13\n1.2.3.4:1080:a:p\n5.6.7.8:1080:b:p\ndone\n' \
  | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 14 — 批量添加 VPS 直连节点

One line: count (1–30). Ports assigned automatically.

```bash
printf '14\n5\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 15 — 修改节点名称

Node index, then new name.

```bash
printf '15\n<IDX>\nNew-Name\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

## 16 — 批量删除节点 🔴

Selection (supports ranges like `1,3,5-7`), then a `y` confirmation.

```bash
printf '16\n1,3,5-7\ny\n' | ssh <target> 'bash /root/xray_deploy.sh' 2>&1
```

---

## Output files (read these to retrieve results)

| File | Contents |
|------|----------|
| `/root/xray_nodes_info.txt` | VLESS links for all nodes (`type=xhttp`/`path`/`mode`, perms 600) |
| `/root/xray_subscription.txt` | Base64 subscription content (perms 600) |
| `/usr/local/etc/xray/config.json` | Live Xray config |
| `/root/.xray_monitor.conf` | Monitor/email config |

## When a prompt sequence looks wrong

The user maintains this script; prompts may evolve. If a run's output shows an unexpected question or the action didn't complete, read the current prompt order straight from the deployed script:

```bash
ssh <target> 'grep -n -A40 "<function_name>()" /root/xray_deploy.sh'
```

Function names map to menu items: `collect_nodes`(1), `add_node`(2), `delete_node`(3), `change_port`(4), `show_status`(5), `show_traffic`(6), `troubleshoot`(7), `update_xray`(8), `monitor_menu`(10), `uninstall`(11), `add_direct_node`(12), `add_batch_nodes`(13), `add_batch_direct_nodes`(14), `rename_node`(15), `batch_delete_nodes`(16) — same names as the Vision variant, since this script is a derivative.
