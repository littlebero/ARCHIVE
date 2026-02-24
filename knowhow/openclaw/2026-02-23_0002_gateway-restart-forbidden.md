# Never Use gateway restart — Use stop → start Instead
- Date: 2026-02-23
- Node: SURFY
- Trigger: F3 (gateway), T5 (rollback procedure)

## Symptom
openclaw gateway restart leaves gateway in unstable state.

## Root Cause
The restart command is unreliable when the gateway is already in a bad state.

## Correct Procedure
openclaw gateway stop
openclaw gateway start
If stop hangs:
netstat -ano | findstr :18789
taskkill /PID {pid} /F
openclaw gateway start

## Required Procedure After Any Config Change
Save → gateway stop → gateway start → Telegram /new → test → check logs

## Rule
❌ Never use gateway restart
✅ Always use gateway stop then gateway start
