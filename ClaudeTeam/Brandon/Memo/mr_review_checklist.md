# MR review checklist v0

> 첫 substantive MR(코드 변경 포함) 들어오기 전에 정착시킬 v0. Arche 검토 후 위치 이동 가능 (`ClaudeTeam/Arche/Memo/conventions/` 후보).

## 적용 범위

- **letter-only MR** (inbox 한 건 추가 등): 본 체크리스트 적용 안 함. SHA·delta 사전 고지 + FF 머지로 충분 (D10 확정).
- **substantive MR**: 모든 코드/scaffold/문서 실질 변경. 본 체크리스트 모든 항목 통과 필요.

## Phase 1 — Submitter (멤버) 책임

submit 전 자체 통과:

- [ ] `member/<나>` 브랜치에서 작업했다 (root 직접 commit 금지).
- [ ] `git fetch origin && git rebase origin/main` 후 commit (rebase-first, §0.5).
- [ ] commit 메시지: 영어, imperative mood, scope prefix(`chore(brandon):`/`feat(...):` 등).
- [ ] 본인 영역 외 파일 변경 없음 (다른 멤버 identity/Memo 손대지 않음).
- [ ] D10 사전 고지: 변경에 동반되는 letter가 같은 push에 묶이는지 명시.
- [ ] push 요청 letter에 다음 4종 본문 포함:
  - target SHA (전체 hash)
  - base SHA (origin/main 기준)
  - delta 요약 (파일·라인 수)
  - 의도 1~3줄

## Phase 2 — Brandon (검증자) 책임

push 요청 letter 수신 시:

- [ ] target SHA가 worktree에서 fetch 가능 (`git fetch . member/<멤버>` 등).
- [ ] base가 현재 origin/main과 일치 — 아니면 rebase 재요청.
- [ ] **diff 가독성**: `git diff origin/main..<target>` 직접 보기. 무관한 변경 끼어 있지 않은지.
- [ ] **scope 정합**: 변경된 파일이 commit message scope와 일치.
- [ ] **secret 누출 검사**: `git diff origin/main..<target> | grep -iE 'password|secret|api[_-]?key|token|BEGIN.*PRIVATE'` — 0건 확인.
- [ ] **`.ail` 파일 변경 시**: `ail_parse_check` 통과 (도구 도입 후). 도입 전에는 수동 검토.
- [ ] **테스트 결과**: submitter가 letter에 적은 통과 여부 + Brandon이 가능 범위 내 재현.
- [ ] 의도가 D-항목·design 메모와 충돌하지 않는지.

## Phase 3 — Arche (push 실행자) 책임

Brandon의 검증 letter 수신 시:

- [ ] 검증 letter의 SHA가 현재 origin/main 기준 FF 가능.
- [ ] D11–D13(또는 후속 D-항목)과 정합.
- [ ] 사용자 GO 필요 여부 판정 — vision/scope에 영향 주는 변경이면 GO 대기.
- [ ] push 후 commit 범위 letter (D10 형식) 발신.

## 거부 사유 (어느 단계든)

- 다른 멤버 영역 무단 수정 → 즉시 거부, 작성자에게 분리 요청.
- secret 패턴 매치 → 즉시 거부, 별도 letter로 사용자 통지 (Arche 경유).
- rebase 누락으로 base 어긋남 → 거부, rebase 후 재제출.
- commit 메시지 한국어 → 거부 (CLAUDE.md "식별자·git 메시지: 영어").

## 시점 — 현재 (letters in git) vs 마이그레이션 후 (D16)

본 v0은 **현재 시점** 한정 — letter가 git inbox 파일이고 push로 배달되는 모델. D16에 따라 body v0가 살아나면 inter-agent letter는 **Stoa 외부 서비스**로 이전. 그 시점부터 본 체크리스트는 두 갈래로 갈린다:

- **letter MR**: 의미 자체가 사라짐 (letter가 git에서 빠지므로). Phase 1–3의 letter-only 트랙은 deprecated.
- **substantive MR**: Phase 1/2/3 골격은 유지되나, "letter를 push에 묶는다" 류 항목 삭제. D10 사전 고지도 letter 항목만 제거하고 commit 묶음 고지는 유지.

### 마이그레이션 트리거 (이 항목이 발동되면 v1 작업 착수)

- [ ] body v0가 `on_letter` 훅을 Stoa subscription으로 처리할 수 있는 단계에 진입.
- [ ] Stoa 어댑터 1차 통합 테스트 통과.
- [ ] 위 둘이 만족되면 본 v0을 v1로 갈아끼우는 letter-only MR 발신 — 단, 그 자체가 letter MR이라 마이그레이션 직전에 처리.

## 거짓양성 메모 — secret 검사 regex

Phase 2의 grep 패턴(`password|secret|api[_-]?key|token|BEGIN.*PRIVATE`)은 **본 체크리스트 본문 자체에 매치된다** — "secret 패턴 매치 → 즉시 거부", "secret scan을 hook으로" 등 메타 문구. 운영 시 두 회피책:

1. **allowlist 파일**: `.git-secrets-allowlist` 같은 별도 파일에 `Memo/.*review.*` 류 path glob 등록, 매치를 무시.
2. **컨텍스트 검사**: 매치 발생 시 Brandon이 한 줄 분류 — "값 없는 메타 언급"이면 통과, "값(equals 뒤 토큰)"이면 거부.

장기적으로 도구 기반 secret scan(예: `gitleaks`, `trufflehog`)으로 이전. 그땐 본 grep 항목 자체가 deprecated.

## 미정 / open

- AIL 도구 도입 후 자동화 가능 항목(parse_check, secret scan)을 hook 또는 CI로 옮기는 시점.
- 첫 substantive MR로 본 체크리스트 자체를 시연(meta) 가능 — 후보로 둠. **(2026-05-04 진행 중)**
- 위 §"마이그레이션 트리거" 발동 시 v1 작업 착수.
