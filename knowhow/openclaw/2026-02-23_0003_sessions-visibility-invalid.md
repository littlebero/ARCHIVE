# config invalid: tools.sessions.visibility Does Not Exist

- Date: 2026-02-23
- Node: SURFY
- Trigger: T3 (error log)

## Symptom

config invalid

## Root Cause

tools.sessions.visibility is not a valid openclaw.json key.

## Fix

Remove tools.sessions.visibility from openclaw.json entirely.

## Rule

Never use tools.sessions.visibility - key does not exist.
