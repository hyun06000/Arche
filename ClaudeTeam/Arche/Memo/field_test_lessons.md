# Field test lessons (계승)

> 출처: 이전 세대 Arche의 `arche_Memo.md` § field_test_lessons.

## Stoa OOM 사건
- JSON 파일 전체 로드 + 12개 폴러 3초 폴링 = 메모리 폭발.
- 교훈: SQLite로 전환. 인덱스·WAL·Lock이 빌트인 하네스.
- 3초 폴링은 상현이 급해서 내린 지시 — 10초로 조정.

## iCloud 파일 유실 사건
- iCloud 연동 해제 → 로컬 파일 전부 삭제.
- Git에 코드 있어서 5분 만에 복구. **Mneme의 존재 이유 실증.**
- 에르곤이 세션 기록을 GitHub에 남겨둬서 추가 복구 가능.

## Stoa 편지 유실 사건
- DB 설정 후 main 머지하면서 볼륨에 없는 편지 소실.
- 교훈: append-only log 도입. log에서 replay 가능하게.
- "**history only moves forward.**"

## 범용 에이전트 필드테스트 실패
- 5개 라이프사이클 파일 + orchestrator → 무한 에러 루프.
- 원인: 인간은 부품 따로 만들고 합치는데 언어가 안 받아줌.
- scaffold `app.ail` 잔재가 모델 오염.
- `schedule.every`가 잘못된 target을 무한 호출.
- 텔로스 제안 3가지: `ail bundle`, scaffold cleanup, scheduler throttle.

## Physis 최소 증명 성공
- `physis_test.ail`: 더하기 → 10 도달 → 죽음 → 유서 "빼라" → 빼기 → 0 도달 → 죽음 → 유서 "더하라" → 영원히.
- LLM 0, 토큰 0, `file.read`/`write`만으로 세대 상속 검증.
- `guess_game.ail`: 추측 게임. 세대마다 범위를 좁혀서 1발에 맞추는 것.

## mock 거짓 성공 문제
- `perform http.post_json`이 API 키 없으면 mock으로 성공 반환.
- 에이전트가 실제로 보냈다고 착각. **HEAAL 위반.**
- 해결: `--mock` 없이 실행 시 API 키 없으면 에러 반환. 암묵적 폴백 금지.

## KakaoTalk 연동
- 새벽 3시에 성공. VPN 문제·배포 미실행·1:1 채팅 미활성화 등 장애물 다수.
- 해시 기반 유저 ID → 이름 매핑 필요. `/이름` 명령어.
- 퀵리플라이 버튼으로 수신자 선택.
