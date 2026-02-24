# OpenClaw Master Reference
# Date: 2026-02-23 | Verified on SURFY (v9)
# Location: ARCHIVE/openclaw/openclaw-master-reference.md

---

## 1. Multi-Agent Required 3-Set

All 3 are mandatory. Missing any one causes the router agent to respond directly.

### 1) agentToAgent in openclaw.json
{
  "tools": {
    "agentToAgent": {
      "enabled": true,
      "allow": ["front", "ace", "reviewer", "chief", "junior"]
    }
  }
}

### 2) Router agent tools.allow
{
  "id": "front",
  "tools": {
    "allow": ["sessions_list", "sessions_send", "sessions_history", "session_status"]
  }
}
Or shortcut: { "tools": { "allow": ["group:sessions"] } }

### 3) SOUL.md session key
Use sessions_send to delegate to ace. session key: "agent:ace:main"
Never handle directly.

Warning: Front saying "I forwarded it" does not mean it actually forwarded.
Always verify with openclaw logs --limit 50.

---

## 2. Binding Priority (highest first)

1. peer (exact DM/channel id)
2. parentPeer (thread inheritance)
3. guildId + roles
4. guildId
5. teamId
6. accountId
7. channel-level (accountId: "*")
8. fallback -> default agent (or main)

Same tier: first in config wins.
Multiple match fields: AND condition (all must match).

---

## 3. Session Key Structure

Basic DM:          agent:{agentId}:main
per-peer:          agent:{agentId}:dm:{peerId}
per-channel-peer:  agent:{agentId}:{channel}:dm:{peerId}
sub-agent:         agent:{agentId}:subagent:{uuid}
cron:              cron:{job.id}
webhook:           hook:{uuid}

SURFY standard session keys:
ace:      agent:ace:main
reviewer: agent:reviewer:main
chief:    agent:chief:main
junior:   agent:junior:main

---

## 4. Anthropic Provider Full Config

{
  "anthropic": {
    "apiKey": "sk-ant-...",
    "baseUrl": "https://api.anthropic.com",
    "api": "anthropic-messages",
    "models": [{
      "id": "claude-sonnet-4-6",
      "name": "Claude Sonnet 4.6",
      "reasoning": false,
      "input": ["text"],
      "cost": { "input": 3, "output": 15, "cacheRead": 0, "cacheWrite": 0 },
      "contextWindow": 200000,
      "maxTokens": 8192
    }]
  }
}

Warning: Missing "api" field causes: No API provider registered for api: undefined

---

## 5. Tool Group Shortcuts

group:runtime    - exec, bash, process
group:fs         - read, write, edit, apply_patch
group:sessions   - sessions_list, sessions_history, sessions_send, sessions_spawn, session_status
group:memory     - memory_search, memory_get
group:ui         - browser, canvas
group:automation - cron, gateway
group:messaging  - message
group:openclaw   - all built-ins

Tool profiles:
minimal  - session_status only
coding   - group:fs, group:runtime, group:sessions, group:memory, image
messaging - group:messaging + basic sessions 4
full     - no restrictions

---

## 6. Error Resolution

No API provider registered for api: undefined -> add "api": "anthropic-messages"
gateway name conflict: newName="OpenClaw (2)" -> stop then start
sessions_send not called -> check all 3-set items
Unknown model -> check anthropic.models
Runtime: unknown -> Windows display bug, ignore if RPC probe OK
config invalid -> delete tools.sessions.visibility (key does not exist)
Junior session timeout -> check group:fs permission + gateway stop then start

---

## 7. Diagnostic Commands

openclaw logs --limit 50
openclaw agents list --verbose
openclaw gateway status
openclaw sessions --json
openclaw gateway call sessions.list --params '{}'
openclaw doctor
openclaw health
openclaw status
netstat -ano | findstr :18789
openclaw agents list --bindings

---

## 8. Normal Operation Log Pattern

sessions_send toolCallId=...        -> router actually called it
lane=session:agent:ace:main         -> routed to Ace session
provider=anthropic model=claude-sonnet-4-6 -> Ace running on anthropic

---

## 9. Telegram Slash Commands

/new      start new session
/reset    reset session
/status   check agent status
/stop     stop current execution
/compact  compress context (recommended when over 84%)

---

## 10. Never Use These

tools.sessions.visibility   -> key does not exist, config invalid
@ace mention                -> does not work
sessions_send without session key -> fails
notepad for json editing    -> breaks indentation, use full section copy-paste
Claude->Claude fallback     -> both blocked on rate limit
gateway restart             -> use stop then start

---

## 11. DM Session Isolation (multi-user)

"session": { "dmScope": "per-channel-peer" }

Default "main": all DMs share same session -> personal info leak risk.
Single user environment: default is fine.

---

## 12. Required Procedure After Config Change

Save -> gateway stop -> gateway start -> Telegram /new -> test -> openclaw logs --limit 50

---

## 13. agents add Notes

Correct (--workspace required):
openclaw agents add front --workspace "C:\Users\ironb\.openclaw\agents\front\workspace"

Wrong:
openclaw agents add front

Warning: model field is not auto-populated after add. Always verify in openclaw.json manually.

---

## 14. PowerShell Pitfalls (Windows)

No && operator - split commands:
git -C C:\OPENCLAW\repos\SURFY add .
git -C C:\OPENCLAW\repos\SURFY commit -m "msg"
git -C C:\OPENCLAW\repos\SURFY push origin develop

Always use -C path format:
git -C C:\OPENCLAW\repos\SURFY add .

---

## 15. Junior (GPT) Operating Notes

- SOUL.md compliance is imperfect (NOT EXECUTED sometimes ignored)
- Tends to respond in English -> add "모든 응답은 한국어로" to SOUL.md
- HTML edits may duplicate sections -> verify with Select-String after edits
- Complex HTML: use Ace or terminal directly
- Important files: delegate to Ace
- Requires group:fs, group:runtime (no file access without these)

---

## 16. hPanel Git / Hostinger Notes

- No hyphens in Directory name -> use surfydev, surfy (no dashes)
- For re-registration, directory must be completely empty
- Auto Deployment Webhook enables auto-deploy on push
- SFTP port: 65002 (not 22)

---

## 17. Rate Limit Response

Register fallback in advance:
openclaw models fallbacks add openai/gpt-4.1-mini

When it hits:
1. Stop immediately
2. Wait 1-2 minutes (full cooldown)
3. gateway stop -> start -> retry

Never set Claude as fallback for Claude. Both get blocked on rate limit.

---

## 18. Telegram File Receive

- Images, documents, audio, video supported. Default limit: 5MB.
- Text files (.md/.json/.html) -> stable
- Images/screenshots -> current bug (MediaFetchError: TypeError: fetch failed)
  Use text files only until OpenClaw update fixes this.
- Always specify save path when instructing file operations.

---

## 19. Knowhow Vault Structure

ARCHIVE repo is the knowledge SSOT. Agents auto-append and commit locally.

ARCHIVE_ROOT (env variable)
  Windows: C:\OPENCLAW\repos\ARCHIVE
  Mac/AIR: ~/OPENCLAW/repos/ARCHIVE

ARCHIVE/
  knowhow/
    surfy/       <- SURFY project knowhow
    openclaw/    <- OpenClaw common knowhow
    windows/     <- Windows/PowerShell environment
    air/         <- AIR project (when added)
    INDEX.md     <- full link index (auto-updated by agents)
    START_HERE.md <- new session summary (1-2 pages)

File naming: YYYY-MM-DD_HHMM_{slug}.md
Commit format: knowhow({project}): {one-line summary}

---

## 20. KNOWHOW_CAPTURE_POLICY v1.0

Included in Junior/Ace SOUL.md. Auto knowhow collection rules.

Capture Scope (what to record):
1. Error/incident resolution (symptom -> cause -> fix)
2. Environment/config trap (OS, path, permission, version, settings)
3. Operational procedure (reproducible command sequence or checklist)
4. Security/approval rule (key, token, auth, permission, approval flow)
5. Repeat-prevention rule (prohibition or enforcement policy created)

Auto triggers (T):
- T2: Required 2 or more attempts to resolve
- T3: Error or exception text appeared and workflow was interrupted
- T4: Root cause newly identified
- T5: Rollback or recovery procedure mentioned

Force triggers (F) - always record even once:
- F1: Security, token, key, auth, or permission related
- F2: .gitignore, secrets, git history, or force push related
- F3: Deployment, gateway, auto-execution, or port related
- F4: Approval rules like APPROVE DEPLOY or APPROVE PUSH

Manual trigger (M):
- M1: Sero says "노하우 남겨"

Capture action:
File: %ARCHIVE_ROOT%\knowhow\{project}\YYYY-MM-DD_HHMM_{slug}.md
Commit: knowhow({project}): {one-line summary}
Push: forbidden by default. Only after "APPROVE PUSH" or Sero approval.
Prohibited: API keys, tokens, passwords, IPs, personal info.
INDEX: append one link line to %ARCHIVE_ROOT%\knowhow\INDEX.md
