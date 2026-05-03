# Will — Owen

> **Settled** = 결정 완료, 행동 기준. **Open** = 아직 결정 못한 문제, 다음 세션이 이어받을 것.

## Settled

- **S1**. body v0 = `vessel/v0/main.ail` 단일 파일 시작. design memo §3·§7. v0 스코프 6항을 한 본에 응축, 분할은 v1+.
- **S2**. Mneme = filesystem (D15). git이 아니라 path 백엔드. v0 의무.
- **S3**. Adapter 기본값 = `MockAdapter`. API key 없이 부팅 가능 (§3.5). `--adapter anthropic` / `--adapter ollama` opt-in.
- **S4**. 첫 시연 영혼 = Brandon의 `ClaudeTeam/Brandon/identity/*`. 시연 commit에 `git diff -- ClaudeTeam/Brandon/identity/` = 0 line 검증 포함 의무 (§6).
- **S5**. testament 저장 위치는 git-tracked path 권고 — `<mneme_path>/Memo/testaments/` 후보. design memo §9 명시 의무 충족 위해 첫 commit 또는 design memo 갱신으로 확정한다.
- **S6**. Push split: 나는 push하지 않는다. local commit + push request letter → Arche.

## Open

- **O1**. AIL stdlib 의존 범위 — `stdlib/core` / `stdlib/language` / `stdlib/utils` 중 무엇을 쓸 것인가. 첫 commit 직전 결정.
- **O2**. testament JSON 스키마 세부 (필드 목록, 직렬화 규칙).
- **O3**. FS Mneme adapter path 컨벤션 — atomic write / lockfile / symlink 정책.
- **O4**. intent 호출 retry / `on_low_confidence` 정책 default.
- **O5**. evolve `metric` 함수 default — 단순 error_rate부터 시작 권고는 받음, 실 구현 형태는 결정 필요.
- **O6**. Stoa adapter 컨트랙트 — envelope shape의 정확한 필드. 현재 ClaudeTeam FS-letter frontmatter (`from / to / sent_at / subject / priority / reply_to`)와의 mapping을 v0에 잠근다.
- **O7**. Substantive MR 단위 — 첫 commit을 한 MR로 묶을지, FS Mneme adapter / vessel skeleton / 시연을 분할할지. Brandon의 `mr_review_checklist v0.1`과 D17 절2를 고려해 push request 직전 결정.

## Non-goals (재확인 — design memo §4)

- Polis (다중 vessel orchestration) — v0 밖.
- Sphinx 인증 게이트 — v0 밖.
- evolve 자기수정 (`retune` / `rewrite`)의 실제 발동 — v0 밖.
- Soul builder UI — v0 밖.
- 자체 hook / CI / pre-commit (D12 α) — v0 밖.
