# N1 — `.ail/` path policy 정찰

> 정찰 메모. 결정 권한 없음. design 메모와 사용자 승인 후 정식화.

## 현재 상태

- `.gitignore` L1–L2:
  ```
  # Agentic AIL project state — local to this checkout.
  .ail/
  ```
  → `.ail/` 디렉토리는 현재 **체크아웃 로컬 상태** 취급 (캐시·작업 파일).
- repo 어디에도 실제 `.ail` 파일이나 `.ail/` 하위 자산 없음 (find 결과 0건).

## 의도 분리 — `.ail/` (디렉토리) vs `.ail` (확장자)

이 둘은 의미가 완전히 다르다 — 이 구분이 모든 정책의 기반:

| 경로 | 의미 | gitignore 대상? |
|------|------|----------------|
| `.ail/` (루트의 단일 디렉토리) | "프로젝트 state, local cache" — 도구가 만드는 산출물 | 예 (현재 그렇게 설정) |
| `*.ail` (확장자) | application source code (D11–D12 핵심 자산) | **절대 아니다** (커밋 대상) |
| `<anywhere>/.ail/` (서브 디렉토리) | 모호 — 하위 모듈에도 캐시가 생길 수 있다면 의도적 | 정책 결정 필요 |

## 위험 — 현재 패턴이 잡는 함정

`.gitignore`의 `.ail/`은 **루트 anchor가 없다** (`/.ail/`이 아님). 즉 repo 안 어느 깊이든 `.ail/` 디렉토리는 모두 무시된다. 만약 향후 `src/foo/.ail/`이 의도적으로 커밋해야 할 자산이라면 무음 누락이 발생.

## 권고 (design 메모에서 결정 부탁)

1. **루트 anchor 명시**: `.ail/` → `/.ail/`로 좁힌다. 의미: "루트의 .ail/ 디렉토리만 로컬 state."
   - 비용: 0. 향후 누락 사고 예방.
2. **소문자 `*.ail` 확장자 정책 명문화**: `*.ail`은 source code, 어디 있어도 커밋 대상. (이미 ignore에 잡히지 않으니 코드 변경 불필요. 정책 문서화만.)
3. **하위 `.ail/` 디렉토리 정책**: 잠정적으로 ignore 대상 아님. 도구가 그런 디렉토리를 만들기 시작하면 그때 명시 추가.

## 결정 대기 항목

- [ ] (D??) `.gitignore`의 `.ail/`을 `/.ail/`로 anchor 좁힐지 — design 메모 confirm 후 letter-only MR로 처리.
- [ ] `.ail/` (루트)의 정확한 역할 — 단순 cache인가, AIL 런타임이 쓰는 working state인가, 양쪽인가. 도구 도입 전이라 잠정.

## 종속 — N3에 영향

pre-commit hook이 `.ail` 파일에 작용한다면 위 #1 anchor 정정이 선행. anchor 안 좁히면 hook이 `*/foo/.ail/bar.ail` 같은 경로를 잘못 분류할 가능성.
