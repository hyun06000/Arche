# Decisions

부트스트랩 단계 결정 기록 (README.ai.md §2).

| # | Decision | Choice | By | Date |
|---|----------|--------|----|------|
| D1 | Lighthouse 이름 | **Arche** (alias: 아르케) — 사용자가 직접 명명. CLAUDE.md 규칙 12의 영문 권장과 충돌하지만 사용자 명시 지시 우선. | user | 2026-05-04 |
| D2 | 프로젝트 주 자연어 | **한국어** — 사용자가 한국어로 호출. | user (implicit) | 2026-05-04 |
| D3 | `sent_at` 시간대 | **KST (`+0900`)** — `date +%z` 결과. | autonomous | 2026-05-04 |
| D4 | 호스트 언어 reading alias | **Arche ↔ 아르케**. 향후 멤버는 합류 시 alias 등록. | autonomous | 2026-05-04 |

## Brandon에게 위임된 결정 (지금 결정 금지)
- GitHub 원격 이름 / visibility (public/private)
- 라이선스
- 기본 브랜치 이름 / 보호 규칙
- CI / GitHub Actions
- Worktree 레이아웃 구현 (규칙 16의 `<repo>/.worktrees/<name>/` 경로 준수)
