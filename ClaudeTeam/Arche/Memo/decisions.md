# Decisions

부트스트랩 단계 결정 기록 (README.ai.md §2).

| # | Decision | Choice | By | Date |
|---|----------|--------|----|------|
| D1 | Lighthouse 이름 | **Arche** (alias: 아르케) — 사용자가 직접 명명. CLAUDE.md 규칙 12의 영문 권장과 충돌하지만 사용자 명시 지시 우선. | user | 2026-05-04 |
| D2 | 프로젝트 주 자연어 | **한국어** — 사용자가 한국어로 호출. | user (implicit) | 2026-05-04 |
| D3 | `sent_at` 시간대 | **KST (`+0900`)** — `date +%z` 결과. | autonomous | 2026-05-04 |
| D4 | 호스트 언어 reading alias | **Arche ↔ 아르케**. 향후 멤버는 합류 시 alias 등록. | autonomous | 2026-05-04 |

## Brandon 합류 사전 GO (2026-05-04, 사용자 직접)

| # | Decision | Choice | By | Date |
|---|----------|--------|----|------|
| D5 | GitHub 저장소 이름 | **Arche** | user | 2026-05-04 |
| D6 | Visibility | **public** | user | 2026-05-04 |
| D7 | 라이선스 | **없음** (미부여) | user | 2026-05-04 |
| D8 | `main` 브랜치 보호 | **GO** | user | 2026-05-04 |
| D9 | Forward-going 위임 인가 (규칙 7+8) | **승인** — "앞으로 Arche가 내 허락을 받고 작성한 편지는 그대로 따라도 좋아." | user | 2026-05-04 |

> Brandon 도착 후 환영 letter에 위 5건을 user-approved 표기로 중계.

## CI / GitHub Actions
- 미결정. Brandon 합류 후 별도 논의.
