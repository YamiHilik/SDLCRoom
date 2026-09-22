---
name: send-push-to-user
description: Отправка пуша пользователю. 
---

Для отправки пушей с нотификациями пользователю используй утилиту `agent-notify`

### Документация по утилите

Usage: agent-notify [--title TXT] [--priority min|low|default|high|urgent]
[--channel inbox|banner] [--markdown] [BODY]
... | agent-notify [options]

Sends a notification to the macOS notification receiver (Hammerspoon)
listening on $NOTIFY_HOST:$NOTIFY_PORT (defaults: host.docker.internal:7777).

Options:
--title TXT        Notification title (optional).
--priority LEVEL   min | low | default | high | urgent (default: default).
--channel CH       inbox (default, popover + history) | banner (transient).
--markdown         Treat body as markdown (inbox channel only).
-h, --help         Show this help.

BODY is taken from the first positional argument, or read from stdin
if none is given. Exit code is always 0 on best-effort delivery.

Examples:
echo "done" | agent-notify --title "build"
agent-notify --title "progress" --channel banner "50% done"
agent-notify --title "result" --priority high --markdown <<'EOF'
## ok
processed **42** files
EOF