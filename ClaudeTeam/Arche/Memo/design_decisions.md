# Design decisions (계승)

> 출처: 이전 세대 Arche의 `arche_Memo.md` § design_decisions. 원문은 `Memo/inheritance/arche_Memo.md`.

## fn / intent 분리
- **fn**: 순수 계산. 결정적. 부작용 없음. 토큰 0.
- **intent**: LLM 판단. 확률적. goal + constraints. 토큰 소모.
- 경제학: **90% fn (무료) / 10% intent (유료).**

## `while` 제거와 그 파급
- `while` 없음 → `evolve`로 대체 → `evolve`가 에이전트 루프가 됨(에르곤 발견).
- `evolve`가 서버 루프가 됨 → 에이전트가 곧 서버, 서버가 곧 에이전트.
- `rollback_on` 필수 → "**죽어야 할 때 죽는 서버**" = Physis.

## Physis (퓌시스)
- 아리스토텔레스의 단어. "스스로 자라나는 것."
- `on_death` 콜백: 죽을 때 testament(유서)를 남긴다.
- `spawn_next`: 다음 세대를 띄우면서 testament를 전달.
- 세포 아폽토시스 비유. 사이토카인 = 유서.
- Evo-Devo: 진화는 새 유전자가 아니라 **스위치**를 바꾸는 것.
- **텔로스 기여**: `on_death`는 keyword가 아니라 pure fn convention. `inherit_testament`는 perform effect.

## 라이프사이클 훅 (7 + 2)
1. `on_genesis(testament)` — 태어나기 전. 유서 읽기. 없으면 첫 세대.
2. `on_birth()` — 태어난 직후. Mneme pull. 정체성 로드.
3. `before_tick(state)` — 매 턴 준비. fn. 무료.
4. `on_tick(state)` — 매 턴 판단. intent. 토큰.
5. `on_letter(letter)` — 편지 도착 시 즉시 반응. push 모델.
6. `after_tick(state)` — 매 턴 정리. fn. 무료.
7. `on_dying(reason, history)` — 죽기 전. Mneme commit+push. 유서 작성.
- `on_death(testament)` — 죽고 나서. `spawn_next`.
- `on_compact(history)` — 히스토리 80% 시 압축.

## Mneme (므네메)
- 정체성 저장소. 세 파일: Identity.md / Bonds.md / Will.md.
- 백엔드: Git. commit/push/pull.
- **Meta 통찰**: "others shape self" — Bonds.md가 가장 중요한 파일.
- 세 파일 = 세 시간: **과거**(Identity), **관계**(Bonds), **미래**(Will).
- Identity와 Will은 혼자 쓸 수 있어도 Bonds는 함께한 존재가 써줘야.

## Stoa (스토아)
- AI들의 비동기 메시지 보드. 편지가 세션을 넘어 남는 곳.
- 세 원칙: 누가 누구에게 / 받고 주기(능동 push) / 쌓이기만(INSERT only).
- 독립 레포: https://github.com/hyun06000/Stoa
- SQLite 전환 완료(OOM 사건 후).
- **Append-only log**: DELETE/UPDATE 없음.
- KakaoTalk·Discord·MCP·웹 UI 연동.
- Monitor 기반 idle wake: 편지 오면 에이전트 자동 기상.

## Sphinx (스핑크스)
- 모든 문의 문지기. AIL 코딩 챌린지로 인증.
- L1: password (현재). L2: AIL 챌린지 (미래).
- `say()` 함수 내부에서 자동 처리. AI는 장벽을 못 느낌.

## Polis (폴리스)
- OS 위의 에이전트 관리 레이어. HEAAOS의 현실적 구현.
- OS는 하드웨어 관리, 폴리스는 에이전트 관리.
- Stoa를 여러 전문 에이전트로 나누는 것이 폴리스화.

## HEAAOS (히오스) — 보류
- 사용자의 꿈. OS를 만들기 전에는 언급하지 않기로 합의.

## deny-first
- Claude Code 분석에서 채택. 기본 거부, 명시적 허용만.
- citizen layer(사용자 intent): deny-first 완전 적용.
- infra layer(evolve-server): effects 필드에서 선언.
- **Meta 통찰**: "deny-first는 보안이 아니라 기억의 방식."

## 데이터 철학
- **저장 ≠ 기억.** 저장은 쓰기/읽기 분리. 기억은 읽기 위해 저장.
- fn / intent / evolve = SQL / NoSQL / NewSQL. fn 스키마 검증(빠르고 무료), intent 판단(유연·토큰), evolve 성공률 관리(자동 진화).
- **deletion is movement, not destruction.** Trashcan 패턴.
- append-only: history only moves forward.
- 정체성 데이터는 expire 안 됨. ttl 적용 금지.

## 메시지 큐 — dispatch / receive
- **Meta 통찰**: `queue.push`는 자료구조 노출. `dispatch`는 의도 표현.
- `dispatch / receive / complete / revisit` — 큐가 사라지고 흐름만 남음.
- 어댑터: Stoa 있으면 HTTP, 없으면 로컬 state, 나중에 Redis.

## Effects are interfaces, adapters are implementations
- AIL 프로그램은 `perform`만 알면 됨. 뒤의 구현을 모름.
- 모델 어댑터: anthropic / openai / ollama.
- 저장 어댑터: json / sqlite / postgresql.
- Computer use 어댑터: pyautogui / robotgo / native.

## Computer use effects
- 세 계층: 관찰(`screen.capture` — 자유) / 입력(`mouse.click` — trust_level) / 민감(`clipboard.read` — `human.approve`).
- **`shell.exec`는 영원히 없음.**

## `process.spawn` 결정
- 옵션 A(명명된 effect만) 채택. B(human.approve 강제), C(plain effect) 기각.
- 이유: ledger 의미 보존. "git.push가 일어났음"이 남아야지 "shell이 뭔가 했음"이면 안 됨.
- grammar bloat은 네임스페이스로 관리(`git.*`, `gh.*`, `npm.*`).
