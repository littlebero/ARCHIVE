# API 키 채팅 노출 사고 대응 절차
날짜: 2026-02-25 | 프로젝트: openclaw | 트리거: F1 (강제)

## 증상
PowerShell 실행 결과를 그대로 복사해서 채팅에 붙여넣는 과정에서
실제 Anthropic API 키값이 채팅에 노출됨.

## 원인
PowerShell은 명령어 실행 시 입력값을 echo하지 않음.
그러나 터미널 전체 내용을 복사할 때 입력한 명령어(키값 포함)가 함께 복사됨.
인지하지 못하고 전체 선택 후 붙여넣기.

## 즉시 대응 절차 (노출 발생 시)
1. Anthropic 콘솔 즉시 접속: https://console.anthropic.com
2. API Keys 메뉴 -> 노출된 키 Disable 또는 Delete
3. 새 키 발급
4. 즉시 교체:
   openclaw config set models.providers.anthropic.apiKey '새로운_키'
5. gateway stop -> start
6. 텔레그램 정상 작동 확인

## 재발 방지
- 키값 포함 명령어 실행 후 채팅 붙여넣기 금지
- 터미널 결과 공유 시 키값 라인 반드시 삭제 후 공유
- 키값 입력은 항상 Sero가 직접 터미널에서 처리
