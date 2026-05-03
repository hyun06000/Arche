# N3 — `ail_parse_check` pre-commit hook 프레임워크 비교

> 도입 결정은 AIL 파서 도구가 실재한 다음. 지금은 후보 비교 + 통합 비용 사전 측정.

## 가정

- `ail_parse_check`(가칭) = 단일 명령. 인자: `.ail` 파일 경로(들). 종료코드: 0=OK, ≠0=parse fail.
- D12 잠정 (α): hook 자체는 비-AIL(셸/Python) 허용.
- 멤버 worktree마다 hook이 동작해야 한다 — 이게 핵심 제약 (아래 §worktree 참조).

## 후보 비교

### 옵션 A — 순정 `.git/hooks/pre-commit` (셸 스크립트)

```sh
#!/bin/sh
# .git/hooks/pre-commit
files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.ail$')
[ -z "$files" ] && exit 0
ail_parse_check $files
```

- **장점**: 무의존. repo 외부 도구 0개.
- **단점**: `.git/hooks/`은 **체크아웃 로컬** — 커밋 대상 아님. 멤버마다 수동 설치 필요. 누락 가능성 큼.
- **회피책**: repo에 `scripts/install-hooks.sh`를 두고 ONBOARDING §1에 한 줄 추가 — "합류 직후 `bash scripts/install-hooks.sh` 실행". 수동이라 신뢰성 낮음.

### 옵션 B — `pre-commit` framework (Python 기반, ~70k★)

```yaml
# .pre-commit-config.yaml (커밋 대상)
repos:
  - repo: local
    hooks:
      - id: ail-parse-check
        name: ail parse check
        entry: ail_parse_check
        language: system
        files: '\.ail$'
```

- **장점**: 설정 파일이 commit됨 → 멤버 합류 시 `pre-commit install` 한 번. CI에서도 동일 설정 재사용 가능. 가장 표준화된 도구.
- **단점**: Python 의존. 멤버마다 `pip install pre-commit`. D12 (α)면 OK, (β)면 부담.
- **worktree 동작**: hook은 main repo `.git/hooks/`에 설치 → 모든 worktree가 공유 (default). 멤버는 자기 worktree에서 commit 시 자동 발동.

### 옵션 C — `lefthook` (Go 단일 바이너리, ~6k★)

```yaml
# lefthook.yml (커밋 대상)
pre-commit:
  parallel: true
  commands:
    ail-parse:
      glob: "*.ail"
      run: ail_parse_check {staged_files}
```

- **장점**: Go 바이너리 1개 — Python 의존 없음. 빠름. 병렬 실행 기본.
- **단점**: 채택률 낮음. 멤버가 `brew install lefthook` 또는 동급 필요.

### 옵션 D — `husky` (Node 기반)

생략. 이 repo가 Node 프로젝트가 아니라 부적합. (현재 `.gitignore`에 `node_modules/`은 있으나 보호적 의미.)

## Worktree 고려사항 (중요)

- Git 기본 동작: 모든 worktree가 main repo의 `.git/hooks/`를 공유.
- 즉 어느 옵션이든 hook 설치는 **한 번만** 하면 모든 멤버 worktree에서 작동.
- 단 `pre-commit install`은 worktree마다 다시 호출해도 idempotent — 위험 없음.
- ONBOARDING §1.5(worktree 발급)에 hook 설치 한 줄 추가 검토 — 내가 worktree 발급 시 동시 처리 가능.

## 권고

**잠정 1순위: 옵션 B (`pre-commit` framework)**.

- 채택률·표준화·CI 재사용 측면에서 최선.
- D12 (α) 가정 하에서만 권고. (β)로 확정되면 옵션 A로 회귀(`.ail` 자체 hook이 가능해지는 시점까지 minimal 셸).

**도입 시점**: AIL 파서 1차 동작 직후. 그 전엔 hook이 검증할 게 없어 noise만.

**시연 사용**: 옵션 B 도입이 첫 substantive MR review 시연의 좋은 후보 — 코드 변경(설정 파일 1개 + ONBOARDING 한 줄) + 테스트(hook 발동 확인) + 의도(D11–D12 정합).

## 결정 대기 항목

- [ ] (D??) AIL 파서 도구 인터페이스 1차 확정 — 그 후 옵션 B/C 중 택일.
- [ ] hook 설치를 worktree 발급 절차(§1.5)에 통합할지.
