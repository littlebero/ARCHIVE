# gateway.auth.token은 .env 분리 대상에서 제외
날짜: 2026-02-25 | 프로젝트: openclaw | 트리거: F3

## 증상
openclaw config set gateway.auth.token 적용 후
실제 토큰값을 어디서 가져와야 할지 불명확.
백업에도 VAR 형식으로 저장됨 -> 복구 불가 상태.

## 원인
gateway.auth.token은 openclaw 내부적으로 자동 생성/관리되는 값.

## 해결
gateway token은 .env 교체 대상에서 제외. 현재 설정 그대로 유지.

## .env 분리 대상 확정
분리 대상: OPENAI_API_KEY, ANTHROPIC_API_KEY, TELEGRAM_BOT_TOKEN
분리 제외: gateway.auth.token (openclaw 내부 관리)

## 재발 방지
.env 분리 작업 시 gateway token은 건드리지 않는다.
