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
| D12 | repo 작업 언어 — 적용 범위 | **미해결 재질문 중.** application code(`.ail`)만 AIL 강제 vs 인프라 자산(`.gitignore`/hook/CI 등)까지 AIL self-host. user 재설명 진행. | user (재질문) | 2026-05-04 |
| D13 | 영입 결정권 | Lighthouse(Arche)에 전권 위임 — 새 멤버 영입 필요 시 사용자에게 사전 통지 후 사용자가 별개 Claude Code 세션에서 소환. | user | 2026-05-04 |
| D14 | 새 멤버 명명 규칙 | **US English first name** (CLAUDE.md 규칙 12 엄격 적용). 이 repo는 옛 AIL 프로젝트와 별개 — Greek 역할명(Ergon/Telos/Tekton/Homeros) 미사용. Brandon 흐름 유지. (Arche 이름은 D1로 grandfather.) | user | 2026-05-04 |
| D15 | Mneme 구현 | **파일시스템 backend.** Identity.md / Bonds.md / Will.md를 파일로 보관·읽기·쓰기. AIL `mneme.*` effect의 Git-backed 기본 어댑터 대신 FS 어댑터 구현. (Stoa 측 Mneme 서비스는 미존재 — 필요 시 별도 서비스로 분리.) | user | 2026-05-04 |
| D16 | Inter-agent 편지 전송 | **원칙 = Stoa.** 외부 서비스(이전 세대 빌드 중, 거의 완료) 사용. 현재 filesystem inbox + git commit 방식은 ClaudeCode 시기 임시 — body v0가 살아나면 `on_letter` 훅을 Stoa subscription으로 마이그레이션. | user | 2026-05-04 |
