# OpenClaw Multi-Agent Setup — Practical Tips

# Date: 2026-02-22 | Public

# For anyone building a multi-agent pipeline with OpenClaw

---

## TIP 1. agentToAgent is OFF by default

You must explicitly enable it. Without this, sessions_send does nothing.

"tools": { "agentToAgent": { "enabled": true, "allow": ["front", "ace"] } }

---

## TIP 2. @mentions don't work between agents

Writing "@ace" in a SOUL.md or message does NOT route to another agent. You must call the sessions_send tool explicitly.

# In SOUL.md, write it exactly like this: Use the sessions_send tool to forward the message to the ace agent.

---

## TIP 3. Always specify the session key in SOUL.md

Without a session key, sessions_send fails with "session key not specified". Session key format: agent:{agentId}:main

Examples:
- ace 1 agent:ace:main
- reviewer 1 agent:reviewer:main
- chief 1 agent:chief:main

In SOUL.md Handoffs: Use sessions_send with session key "agent:ace:main"

---

## TIP 4. Anthropic models must be registered manually

OpenClaw only knows OpenAI out of the box. To use claude-sonnet-4-6 or any Anthropic model, add it to models.providers.

"models": { "providers": { "anthropic": { "apiKey": "sk-ant-your-key", "models": [ { "id": "claude-sonnet-4-6", "name": "Claude Sonnet 4.6", "reasoning": false, "input": ["text"], "cost": { "input": 3, "output": 15, "cacheRead": 0, "cacheWrite": 0 }, "contextWindow": 200000, "maxTokens": 8192 } ] } } }

Without this: Error: Unknown model: anthropic/claude-sonnet-4-6

---

## TIP 5. tools.sessions does not exist

This key looks reasonable but will break your entire config:

// WRONG - do not add this
"tools": { "sessions": { "visibility": "all" } }

You will see: Unrecognized key: "sessions" in logs. Hot reload will be skipped silently. Remove it immediately.

---

## TIP 6. Always reset the session after config changes

A session opened while config was invalid caches the broken state. Even after fixing the config, the old session won't reflect changes.

openclaw gateway restart
Send /new in Telegram
Then test

---

## TIP 7. Debugging checklist when Front responds directly

openclaw logs --limit 50 1 Look for: Unrecognized key / sessions_send call attempts
Check openclaw.json directly (not a summary) 1 tools.agentToAgent enabled? 1 front tools.allow includes sessions_send? 1 models.providers.anthropic registered? Check front SOUL.md 1 Handoffs says "use sessions_send tool"? 1 Session key "agent:ace:main" specified? Send /new in Telegram 1 reset session 
openclaw gateway restart
Test: "What is your agent name?"

---

## Minimum working config summary

openclaw.json: tools.agentToAgent.enabled = true
tools.agentToAgent.allow includes all agents
agents[front].tools.allow includes sessions_send
models.providers.anthropic registered with model id
Front SOUL.md: "Use sessions_send tool to forward to ace"
"Session key: agent:ace:main"
All of the above must be correct simultaneously. Missing any single one causes Front to respond directly.
