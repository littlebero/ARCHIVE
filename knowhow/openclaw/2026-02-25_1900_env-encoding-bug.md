# .env 생성 시 한글 플레이스홀더 인코딩 깨짐 버그
날짜: 2026-02-25 | 프로젝트: openclaw | 트리거: F1

## 증상
PowerShell here-string으로 .env 생성 시 한글이 포함된 플레이스홀더가 깨짐.
openclaw.json 백업에 이상한 문자로 저장됨.
결과적으로 API 키 연결 실패 -> 텔레그램 무응답.

## 원인
PowerShell Out-File -Encoding UTF8의 BOM 처리 문제.
플레이스홀더에 한글 사용이 원인.

## 해결
플레이스홀더 없이 바로 실제 키값 입력.
또는 영문 플레이스홀더만 사용:
ANTHROPIC_API_KEY=YOUR_KEY_HERE

## 재발 방지
.env 생성 시 플레이스홀더는 영문만 사용.
생성 후 반드시 확인:
Get-Content C:\Users\ironb\.openclaw\.env
한글이 깨져 보이면 즉시 재작성.
