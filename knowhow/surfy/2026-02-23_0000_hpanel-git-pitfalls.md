# hPanel Git Deployment Pitfalls
- Date: 2026-02-23 | Node: SURFY | Trigger: T2, T4

## Pitfall 1: No Hyphens in Directory Name
Symptom: deployment fails.
Fix: use surfydev not surfy-dev

## Pitfall 2: Directory Must Be Empty for Re-registration
Symptom: Git registration fails.
Fix: delete all files in directory via hPanel File Manager first.

## Current SURFY Setup
Repository: git@github.com:littlebero/SURFY.git
Branch: develop | Directory: surfydev
SFTP Port: 65002 | FTP Port: 21
