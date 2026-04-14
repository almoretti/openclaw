# Moretti Patch Inventory

Updated: 2026-04-14
Primary maintenance branch: `moretti/main`
Upstream baseline: `origin/main`

## Purpose
This file documents the active local patch set that we intentionally carry in the Moretti fork.
Older feature branches and stashes were historical migration artifacts and have been cleaned up after consolidation.

## Active patch set on `moretti/main`

### 1) WhatsApp monitor-mode config support
Files:
- `src/config/types.whatsapp.ts`
- `src/config/zod-schema.providers-whatsapp.ts`

Behavior:
- `requireMention` accepts `"monitor"` in addition to boolean values.

### 2) Passive monitor-mode group gating
File:
- `extensions/whatsapp/src/auto-reply/monitor/group-gating.ts`

Behavior:
- groups configured with `requireMention: "monitor"` are logged/passively monitored
- they never trigger a reply, even if the bot is mentioned

### 3) Access-control bypass for monitor groups
File:
- `extensions/whatsapp/src/inbound/access-control.ts`

Behavior:
- monitor-mode groups bypass sender-level filtering
- `groupPolicy: "disabled"` still wins
- monitor messages are not marked as read

### 4) Better inbound monitor logging and append/history behavior
File:
- `extensions/whatsapp/src/inbound/monitor.ts`

Behavior:
- enrich inbound logs with sender/group metadata
- append/history-sync messages are not marked as read
- append/history-sync messages do not trigger auto-replies

### 5) Session/community sync support for monitor groups
File:
- `extensions/whatsapp/src/session.ts`

Behavior:
- if any monitor groups exist, enable broader history sync
- attempt community participation sync on connect

### 6) Watchdog timeout increase
File:
- `extensions/whatsapp/src/auto-reply/monitor.ts`

Behavior:
- increases monitor watchdog timeout from 30 minutes to 6 hours
- avoids reconnect loops during quiet periods

## Branch policy
- `origin/main` = upstream source repo
- `fork/main` = clean upstream mirror
- `fork/moretti/main` = deployed/custom branch carrying the patch set above

## Rule going forward
Do not reintroduce ad-hoc local patch branches unless there is a short-lived migration reason.
Commit intended local behavior directly onto `moretti/main` (or a short-lived topic branch that merges back immediately).
