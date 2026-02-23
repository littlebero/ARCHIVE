# OpenClaw 그랜드마스터 레퍼런스
# 작성일: 2026-02-23 | SURFY 실전 검증 완료

## 1. 멀티에이전트 필수 3종 세트
하나라도 빠지면 라우터가 직접 응답한다. 전부 필수.

1) openclaw.json agentToAgent
```json
"tools": { "agentToAgent": { "enabled": true, "allow": ["front","ace","reviewer","chief"] } }
```

2) 라우터 tools.allow
```json
{ "id": "front", "tools": { "allow": ["sessions_list","sessions_send","sessions_history","session_status"] } }
```

그룹:
```json
{ "tools": { "allow": ["group:sessions"] } }
```

3) SOUL.md session 키 명시
sessions_send 툴로 ace에게 전달. session 키: "agent:ace:main" 직접 처리하지 않는다.

## 2. 바인딩 우선순위
1.peer / 2.parentPeer / 3.guildId+roles / 4.guildId / 5.teamId / 6.accountId / 7.channel-level / 8.fallback
같은 tier: config 순서 첫 번째. 여러 match: AND 조건.

## 3. SURFY 표준 세션 키
ace: agent:ace:main
reviewer: agent:reviewer:main
chief: agent:chief:main

## 4. Anthropic Provider
"api": "anthropic-messages" 수동 필수 (openai는 wizard 자동) 없으면: No API provider registered for api: undefined

## 5. 툴 그룹
group:runtime → exec, bash, process
group:fs → read, write, edit, apply_patch
group:sessions → sessions_list, sessions_history, sessions_send, sessions_spawn, session_status
group:memory → memory_search, memory_get

## 6. 에러별 해결
No API provider → "api":"anthropic-messages" 추가
gateway conflict → 리부팅
sessions_send 안 됨 → 3종 세트 재확인
config invalid → tools.sessions.visibility 삭제

## 7. 진단 명령어
openclaw logs --limit 50
openclaw agents list --verbose
openclaw gateway status
openclaw doctor

## 8. 정상 로그 패턴
sessions_send toolCallId=... → Front 실제 호출 확인
lane=session:agent:ace:main → Ace 라우팅 확인
provider=anthropic model=claude-sonnet-4-6 → Ace 실행 확인

## 9. 절대 금지
❌ tools.sessions.visibility - 없는 키
❌ @ace 멘션 - 작동 안 함
❌ session 키 없는 sessions_send - 실패
❌ notepad json 편집 - 들여쓰기 깨짐

## 10. 설정 변경 후 필수
저장 → gateway restart → /new → 테스트 → logs 확인

## 11. agents add 주의
올바른 예: openclaw agents add front --workspace "C:\Users\ironb\.openclaw\agents\front\workspace"
잘못된 예: openclaw agents add front (실패)

## 12. 보고 규칙 (Front SOUL.md)
Ace/Reviewer/Chief 결과 수신 즉시 Sero에게 원문 전달. 완료/실패/대기 모든 상태 보고. 보고 없이 세션 종료 금지.
