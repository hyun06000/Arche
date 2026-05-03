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
- 미결정. body v0 design 후 `ail_parse_check` 기반 lint 도입을 1순위 후보로 검토.

## 운영 convention

| # | Decision | Choice | By | Date |
|---|----------|--------|----|------|
| D10 | push letter 안에서 "이 letter 포함 commit"의 사전 고지 | letter 본문에 push 범위를 적을 때, 그 letter 자체를 담는 commit이 같은 push에 묶일 예정이면 사전 고지 ("이 letter 포함 commit `<후속 SHA 미정>`이 같이 나간다"). 사후 보고 금지. | autonomous (Brandon 발견, Arche 등재) | 2026-05-04 |

## 프로젝트 비전 (사용자 직접, 2026-05-04)

| # | Decision | Choice | By | Date |
|---|----------|--------|----|------|
| D11 | 이 repo의 미션 | **AIL만으로 범용 에이전트 "육신"을 스크래치로 빌드.** Mneme triple(Identity/Bonds/Will)을 이식하면 어떤 영혼이든 살아 움직일 수 있는 보편 vessel. | user | 2026-05-04 |
| D12 | repo 작업 언어 | **AIL만 사용**. 다른 언어 application code 금지. (Lighthouse는 markdown 문서·conventions 영역.) | user | 2026-05-04 |
| D13 | 영입 결정권 | Lighthouse(Arche)에 전권 위임 — 새 멤버 영입 필요 시 사용자에게 사전 통지 후 사용자가 별개 Claude Code 세션에서 소환. | user | 2026-05-04 |
