# Security: secrets.json Must Be in .gitignore
- Date: 2026-02-23 | Node: SURFY | Trigger: F1, F2

## Risk
If secrets.json is not in .gitignore, git push exposes API keys on GitHub.

## Required .gitignore Entries
secrets.json | .env | *.key | *.pem

## Status (2026-02-23)
Confirmed: secrets.json is present in SURFY/.gitignore

## Rule
Always verify .gitignore includes secrets.json before first push on any new repo.
