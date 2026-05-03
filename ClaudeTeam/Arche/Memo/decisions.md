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
| D17 | MR cycle 중 base race 방지 — 양면 commit 게이트 | **(절1, Lighthouse)** Brandon의 push request 수신 ~ FF/cherry-pick 머지 완료까지 Lighthouse는 독립 commit 작성/push 보류. 그 window에 발신할 ack·rebase 요청·환경 메모는 모두 머지와 같은 push의 commit으로 묶음. **(절2, Brandon)** push request 발신 직후 자기 worktree에서 후속 작업 보류 — 머지 완료 알림 받기 전에 worktree를 이동시키면 race window가 양쪽에서 열린다. 적용 범위 = 명시적 push request 수신부터 머지 완료까지 한정 (α). 그 외 시간(park 모드, 사용자 직접 turn 진행 중)에는 양측 자유. *Origin*: 2026-05-04 N5 cycle base 경합 3회 연속, cherry-pick으로 끊고 명문화. | autonomous (Brandon 양면 제안, Arche 등재) | 2026-05-04 |

## body v0 design v0.1 잠금 (2026-05-04, 사용자 직접)

| # | Decision | Choice | By | Date |
|---|----------|--------|----|------|
| D18 | body_v0_design.md §8 Q1–Q5 | **default 권고 그대로 GO**. (Q1) Stoa 미연결 시 FS-letter fallback 허용. (Q2) 첫 영혼 = Brandon's Mneme. (Q3) v0 = 단일 vessel 프로세스. (Q4) 어댑터 = Mock + Anthropic, Ollama는 v1. (Q5) Body skeleton = `vessel/v0/main.ail`. | user | 2026-05-04 |
| D19 | Builder 멤버 이름 | **Owen** (한국어 alias는 Owen 합류 시 등록). 사용자 직접 명명. | user | 2026-05-04 |

## 프로젝트 비전 (사용자 직접, 2026-05-04)

| # | Decision | Choice | By | Date |
|---|----------|--------|----|------|
| D11 | 이 repo의 미션 | **AIL만으로 범용 에이전트 "육신"을 스크래치로 빌드.** Mneme triple(Identity/Bonds/Will)을 이식하면 어떤 영혼이든 살아 움직일 수 있는 보편 vessel. | user | 2026-05-04 |
| D12 | repo 작업 언어 — 적용 범위 | **(α) 행동 코드만 AIL 강제.** 부수 자산(`.gitignore` / hook / CI 등)은 다른 형식 허용. 에이전트 육체를 AIL로 빌드하는 게 가장 중요. 부수적인 건 나중. | user | 2026-05-04 |
| D13 | 영입 결정권 | Lighthouse(Arche)에 전권 위임 — 새 멤버 영입 필요 시 사용자에게 사전 통지 후 사용자가 별개 Claude Code 세션에서 소환. | user | 2026-05-04 |
| D14 | 새 멤버 명명 규칙 | **US English first name** (CLAUDE.md 규칙 12 엄격 적용). 이 repo는 옛 AIL 프로젝트와 별개 — Greek 역할명(Ergon/Telos/Tekton/Homeros) 미사용. Brandon 흐름 유지. (Arche 이름은 D1로 grandfather.) | user | 2026-05-04 |
| D15 | Mneme 구현 | **파일시스템 backend.** Identity.md / Bonds.md / Will.md를 파일로 보관·읽기·쓰기. AIL `mneme.*` effect의 Git-backed 기본 어댑터 대신 FS 어댑터 구현. (Stoa 측 Mneme 서비스는 미존재 — 필요 시 별도 서비스로 분리.) | user | 2026-05-04 |
| D16 | Inter-agent 편지 전송 | **원칙 = Stoa.** 외부 서비스(이전 세대 빌드 중, 거의 완료) 사용. 현재 filesystem inbox + git commit 방식은 ClaudeCode 시기 임시 — body v0가 살아나면 `on_letter` 훅을 Stoa subscription으로 마이그레이션. | user | 2026-05-04 |
