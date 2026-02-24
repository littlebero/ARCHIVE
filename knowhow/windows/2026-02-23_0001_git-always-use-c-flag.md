# Always Use git -C path in Agent Context
- Date: 2026-02-23 | Node: SURFY (Windows) | Trigger: T4

## Root Cause
Agents run from their own workspace. Without explicit path, git operates on wrong repo.

## Fix
git -C C:\OPENCLAW\repos\SURFY add .
git -C C:\OPENCLAW\repos\SURFY commit -m "msg"

## Rule
All git commands must use -C absolute path format.
