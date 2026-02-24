# Error: No API Provider Registered for api: undefined
- Date: 2026-02-23
- Node: SURFY
- Trigger: T3 (error log), T4 (root cause identified)

## Symptom
No API provider registered for api: undefined

## Root Cause
Missing "api" field in the anthropic provider block of openclaw.json.

## Fix
"anthropic": {
  "apiKey": "sk-ant-...",
  "baseUrl": "https://api.anthropic.com",
  "api": "anthropic-messages",
  ...
}

"api": "anthropic-messages" is mandatory.

## Verification
openclaw gateway status
openclaw logs --limit 20
