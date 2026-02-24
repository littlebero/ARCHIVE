# Never Set Claude as Fallback for Claude
- Date: 2026-02-23 | Node: SURFY | Trigger: F1, F3

## Symptom
During rate limit, both primary and fallback are blocked simultaneously.

## Root Cause
Claude to Claude fallback means both hit the same rate limit at the same time.

## Fix
openclaw models fallbacks add openai/gpt-4.1-mini

Recovery: Stop immediately > Wait 1-2 min > gateway stop > start > retry

## Rule
Never set Claude as fallback for Claude.
Fallback must always be a different provider (e.g. gpt-4.1-mini)
