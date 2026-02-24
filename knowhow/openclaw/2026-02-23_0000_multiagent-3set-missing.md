# Multi-Agent Pipeline Fails: Front Responds Directly

- Date: 2026-02-23
- Node: SURFY
- Trigger: F3 (gateway), T4 (root cause identified)

## Symptom

Front agent responds directly instead of delegating to Ace/Junior.

## Root Cause

Multi-agent routing requires all 3 components. Missing any one causes Front to handle requests itself.

## Fix

1) agentToAgent in openclaw.json (top level)
{
  "tools": {
    "agentToAgent": {
      "enabled": true,
      "allow": ["front", "ace", "reviewer", "chief", "junior"]
    }
  }
}

2) Front tools.allow
{
  "id": "front",
  "tools": {
    "allow": ["sessions_list", "sessions_send", "sessions_history", "session_status"]
  }
}

3) Front SOUL.md - session key required
Use sessions_send to delegate to ace. session key: "agent:ace:main"
Never handle directly.

## Verification

openclaw logs --limit 50
Look for: sessions_send toolCallId=...
Look for: lane=session:agent:ace:main

## Warning

Front saying "I forwarded it" does not mean it actually forwarded. Always verify in logs.
