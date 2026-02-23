# OpenClaw 멀티에이전트 세팅 완전 정복

# 삽질 기록과 해결책 — SURFY 프로젝트 실전 노하우

# 작성일: 2026-02-22 | 저장: ARCHIVE/openclaw/

---

## 이 문서는 무엇인가

텔레그램에서 메시지를 보내면 Front 에이전트가 받아서 Ace(코드 담당)로 넘기고, Ace가 GDScript를 짜서 돌려주는 멀티에이전트 파이프라인을 구축하는 과정의 기록이다. 공식 문서만 보면 쉬워 보이지만, 실제로 작동시키기까지 수십 번의 삽질이 있었다. 같은 고생을 반복하지 않도록 실패 순서대로 정직하게 기록한다. 최종 달성한 것: 텔레그램  Front(gpt-4.1-mini)  sessions_send  Ace(claude-sonnet-4-6)  코드 생성  Front   텔레그램

---

## 삽질 1: tools.sessions 유령 키

### 증상

error: Unrecognized key: "sessions"

### 원인

공식 문서를 잘못 읽어서 tools.sessions.visibility 같은 키를 추가했다. 이 키는 실제로 존재하지 않는다.

### 해결

tools 섹션에는 agentToAgent만 넣는다. "tools": { "agentToAgent": { "enabled": true, "allow": ["front", "ace", "reviewer", "chief"] } }

---

## 삽질 2: @ace 멘션은 작동하지 않는다

### 원인

에이전트 간 통신은 sessions_send 툴을 명시적으로 호출해야 한다.

### 필수 3종 세트 (하나라도 빠지면 작동 안 함)

1) tools.agentToAgent.enabled = true

2) front tools.allow에 sessions_send 포함

3) Front SOUL.md에 "sessions_send 툴을 사용해서" + session 키 명시

---

## 삽질 3: agentToAgent 섹션 자체가 없었다

### 진단

cat ~/.openclaw/openclaw.json 으로 원본 직접 확인. 요약본이나 에이전트 말 믿지 말 것.

---

## 삽질 4: 세션 캐시 문제

### 증상

설정을 고쳤는데도 Front가 여전히 직접 응답한다.

### 해결

텔레그램에서 /new 전송  새 세션 시작
설정 변경 후에는 반드시 /new로 세션 리셋.

---

## 삽질 5: sessions_send가 session 키를 몰랐다

### 증상

"세션 키나 라벨이 지정되지 않아 실패했습니다"

### session 키 규칙

agent:{agentId}:main
ace  agent:ace:main
reviewer  agent:reviewer:main
chief  agent:chief:main

### 해결

SOUL.md Handoffs에 명시: sessions_send 호출 시 session 키는 "agent:ace:main" 을 사용한다

---

## 삽질 6: Anthropic 모델 미등록

### 증상

Error: Unknown model: anthropic/claude-sonnet-4-6

### 원인

models.providers에 Anthropic이 없었음.

### 해결

"models": { "providers": { "anthropic": { "apiKey": "sk-ant-...", "models": [{ "id": "claude-sonnet-4-6", "name": "Claude Sonnet 4.6", "reasoning": false, "input": ["text"], "cost": { "input": 3, "output": 15, "cacheRead": 0, "cacheWrite": 0 }, "contextWindow": 200000, "maxTokens": 8192 }] } } }

---

## 핵심 요약

1. agentToAgent 3종 세트: 활성화 + front tools.allow + SOUL.md session 키 명시. 하나라도 빠지면 안 된다.

2. Anthropic 모델은 직접 등록: models.providers.anthropic 섹션 추가 필수.

3. 설정 변경 후 반드시: /new 세션 리셋 + gateway restart.
