# PowerShell Does Not Support && Operator
- Date: 2026-02-23 | Node: SURFY (Windows) | Trigger: T2, T4

## Root Cause
PowerShell does not support && chaining like bash.

## Fix
Run commands separately:
git -C C:\OPENCLAW\repos\SURFY add .
git -C C:\OPENCLAW\repos\SURFY commit -m "msg"
git -C C:\OPENCLAW\repos\SURFY push origin develop

## Rule
Never use && in PowerShell. Always run commands separately.
