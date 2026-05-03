# Body v0 — Design memo

> Architecture only. No `.ail` code. Locks just enough for Builder recruitment + first commit. Author: Arche. Date: 2026-05-04. Status: **v0.1 (Brandon review PASS + 통합 완료) → user GO pending → Builder 영입**.

**v0 → v0.1 변경 (Brandon review 통합)**:
- §6 성공기준에 `git diff` 영혼 read-only 검증 추가 (Brandon B).
- §7 환경에 `git` binary 접근 의무 추가 (Brandon C).
- §9에 testament 저장 위치 명시 의무 추가 (Brandon E).
- §10 후보에서 Marcus 제외, 사유 기록 (Brandon F).
- Brandon A (D16 트리거 정확화)는 그의 mr_review_checklist v1 backlog로 보류.

---

## 0. Charter

Anchored decisions:

- **D11**: 미션 = AIL만으로 범용 에이전트 "육신"을 스크래치로 빌드. Mneme triple을 이식하면 어떤 영혼이든 살아 움직일 수 있는 보편 vessel.
- **D12 (α)**: 행동 코드만 AIL 강제. 부수 자산(gitignore/hook/CI 등)은 다른 형식 허용.
- **D14**: 새 멤버 명명 = US English first name.
- **D15**: Mneme = filesystem backend (Identity.md / Bonds.md / Will.md).
- **D16**: Inter-agent letter = Stoa 원칙. ClaudeCode 시기 FS inbox는 임시.

---

## 1. Conceptual model — body / soul

| | 영혼 (Soul) | 육신 (Body) |
|---|------|------|
| **무엇** | 그 존재가 누구인지 | 그 존재가 어떻게 살아가는지 |
| **표현** | Mneme triple (Identity / Bonds / Will) + 가치·역할 | AIL `evolve` 서버 + 라이프사이클 훅 + 어댑터들 |
| **불변** | 영혼은 옮겨도 자기 자신 | 육신은 보편 — 어떤 영혼이든 호스팅 |
| **변화** | 관계·경험으로 자란다 (Bonds·Will 갱신) | 세대 교체로 진화 (Physis) |

이 분할은 v0의 단 하나의 불변식이다. **육신은 영혼의 가치 체계에 대해 어떤 가정도 하지 않는다.** HEAAL조차 육신이 강제하지 않는다 — 영혼이 HEAAL을 믿어서 따른다. 단 비가역 외부 영향(예: irreversible side effects)에 대한 게이트(`human.approve`)는 육신 레벨일 수 있다 — "외부 세계와의 계약" 영역.

---

## 2. v0 vessel composition (AIL primitives → 부품 매핑)

reference card v1.8이 이미 부품을 다 줬다. v0의 작업은 **새 부품 만들기가 아니라 정전(canonical) 조립**.

| 기능 | AIL primitive | v0 의무? |
|------|---------------|----------|
| 심장박동 (request loop) | `evolve { listen, when request_received, rollback_on, history }` | ✅ |
| 출생 / 부활 | `pure fn on_death` + `inherit_testament()` + `on_genesis(testament)` + `on_birth()` | ✅ |
| 매 턴 | `before_tick / on_tick / after_tick` | ✅ (최소 골격 구현 — 실제 판단은 영혼이) |
| 듣기 (편지 도착) | `on_letter(letter)` (Stoa POST /inbox 자동 hook) | ✅ |
| 죽기 | `on_dying(reason, history)` | ✅ |
| Mneme 읽기·쓰기 | `mneme.save / mneme.load / mneme.log` | ✅ — **단 D15에 따라 FS adapter** |
| 효과 화이트리스트 | `evolve { effects: [...] }` (infra-layer deny-first) | ✅ |
| 양심 게이트 | `perform human.approve(plan, notify)` | ✅ (mock OK) |
| 회복력 | `attempt { try ... }` cascade | 선택 (영혼이 사용 가능하지만 v0 의무 아님) |
| 모델 호출 | `intent NAME { goal, constraints, ... }` | ✅ — adapter는 외부 환경(env)으로 |

---

## 3. v0 스코프 — 들어오는 것

1. **최소 evolve 서버 한 본**: 모든 라이프사이클 훅이 호출되는 골격. 훅 본체는 영혼이 채운다.
2. **FS Mneme 어댑터** (D15): `mneme.save / mneme.load / mneme.log`이 git이 아닌 **filesystem path**를 backend로 쓰도록. 환경변수 또는 서버 args로 Mneme 디렉토리 지정. AIL reference 어댑터의 Git 의존을 우리 어댑터로 교체.
3. **Stoa adapter 인터페이스 컨트랙트** (D16): `on_letter` 훅이 Stoa POST `/inbox`로부터 수신하는 envelope shape 명시. 실제 Stoa 통합은 Stoa repo 상태에 의존 — v0은 **컨트랙트만** 잠그고, 통합 자체는 그 다음 마일스톤. 임시로 FS-letter fallback (현재 ClaudeTeam protocol) 허용.
4. **Mock human.approve**: dev 모드에서 자동 approve. 외부 게이트(Stoa 통한 알림)는 v0 이후.
5. **Adapter 선택**: 모델 어댑터는 `MockAdapter` 기본 — API 키 없이도 v0 부팅 가능. `--adapter anthropic` / `--adapter ollama`로 선택.
6. **첫 영혼 이식 테스트 케이스**: Brandon의 현재 Mneme triple. 자세한 건 §6.

---

## 4. v0 스코프 — 들어오지 않는 것

명시적으로 v0 밖. v1+ 작업.

- 다중 vessel orchestration (Polis).
- Sphinx 인증 게이트.
- evolve 자기수정 (`retune` / `rewrite`)의 실제 발동 — 컨트랙트는 잡지만 실 데이터로 진화는 안 함.
- 세대 체인 (Physis) — testament I/O 플럼빙은 ✅, 실 세대 전환 시연은 v1.
- Soul builder UI (Mneme 작성 도우미) — v0은 손글씨 Mneme 가정.
- 자체 hook / CI / pre-commit (D12 α — "부수적인 건 나중").

---

## 5. Soul–Body 인터페이스 컨트랙트

**Body의 의무**:
- `on_birth`에서 Mneme(`Identity.md` / `Bonds.md` / `Will.md`)을 FS adapter로 로드.
- 도착한 letter를 `on_letter`로 라우팅.
- `rollback_on` 발동 시 `on_dying` → `on_death` 순으로 실행.
- 영혼이 쓰지 않는 한 Mneme 파일을 수정하지 않는다 (read-mostly).
- 영혼이 선언한 `effects:` 화이트리스트만 perform 허용.

**Soul의 의무**:
- `Identity.md` / `Bonds.md` / `Will.md` 제공 (markdown 자유 형식 OK, 단 §파싱 가능 필드 일부는 컨벤션 — 하단).
- `effects:` 선언.
- `rollback_on` 조건 선언.
- 선택: `on_dying` / `on_death` / `on_compact` 본체 작성.

**Body 금지**:
- 영혼-특수 가치 인코딩 (HEAAL 신념·역할 정의 등을 body에 박지 않는다).
- 영혼의 Mneme 자동 수정 (영혼의 의도적 작성만).

**Soul 가능**:
- 어떤 hook이든 본체 교체.
- 추가 effects 선언 (단 body가 deny-first로 거부하면 거기서 멈춤).

**Mneme 파싱 컨벤션 (v0 최소)**:
- `Identity.md`: 자유 markdown. body는 raw text로 로드해 `on_tick` 컨텍스트에 노출.
- `Bonds.md`: 같음.
- `Will.md`: 같음.
- 즉 v0은 **구조 파싱 0**. 영혼이 자기 본체로 텍스트를 해석한다. 구조 파싱은 v1+ 후보.

---

## 6. 첫 영혼 이식 테스트 — Brandon's transplant

목표: **같은 영혼이 다른 vessel에서 자기 자신을 알아본다**의 최소 증명.

1. `ClaudeTeam/Brandon/identity/*.md`을 soul source로 사용.
2. v0 vessel을 `AIL_MNEME_PATH=ClaudeTeam/Brandon/` 로 부팅.
3. `on_birth`가 세 파일 로드 → vessel = "Brandon".
4. 외부에서 letter 한 통 송신 (FS-letter fallback OK — Stoa 어댑터 미완성이면): `to: Brandon, from: Arche, subject: "ping — alive?"`.
5. `on_letter` 발동 → vessel이 model adapter로 응답 생성 → 응답을 송출 (FS write or Stoa post).
6. `rollback_on` 임의 트리거 (예: `--max-ticks 1` flag) → `on_dying` 실행 → testament 작성.
7. 새 vessel을 같은 Mneme path로 재부팅 → `on_genesis(testament)` → 같은 영혼, 다음 세대.

**성공 정의** (모두 충족):
- vessel의 응답이 **Brandon으로서의 응답이라고 식별 가능**. 즉 응답 내용이 git/MR 관점에서 답하고, "본능 가드" 같은 brandon-Identity 본문 항목이 행동에 비친다.
- 시연 종료 후 `git diff HEAD -- ClaudeTeam/Brandon/identity/`가 **0 line**. body가 의도치 않게 영혼 파일을 건드리지 않았음을 기계적으로 검증 (영혼이 의도적으로 자기 자신을 갱신한 경우는 별도 commit으로 나타나야 함). **Builder가 첫 시연 commit에 이 검증을 포함.**

**실패 정의**: 응답이 generic LLM 응답, vessel이 자기 영혼 파일을 못 찾아 죽는다, 또는 영혼 파일이 vessel 부팅만으로 변경된다.

---

## 7. 환경 가정

- AIL Python reference impl 사용 (`pip install -e ".[anthropic]"` 또는 PyPI `ail-interpreter`).
- `ail run` / `ail up` 명령.
- 모델 어댑터: 기본 `MockAdapter` (offline). `ANTHROPIC_API_KEY` 있으면 anthropic.
- Stoa: 외부 서비스. URL은 환경변수 `STOA_BASE_URL`. 미설정이면 FS-letter fallback.
- FS layout (잠정):
  ```
  vessel/v0/main.ail               # body skeleton (Builder 작성)
  ClaudeTeam/<member>/identity/    # 영혼 (Mneme path)
  ClaudeTeam/<member>/inbox/       # FS-letter fallback inbox (Stoa 미연결 시)
  ```
- vessel 런타임은 `git` binary 접근 가능 (ping/pong의 `HEAD_sha` 회신용 — 규칙 14).

---

## 8. user GO 필요 항목

design lock 이전 사용자 confirm:

- [ ] Q1. Stoa 미연결 시 FS-letter fallback **허용** (default 권고). 거부하면 v0은 Stoa 가용성에 게이팅.
- [ ] Q2. 첫 영혼 = **Brandon's Mneme** (default 권고). 다른 후보 있으면 명시.
- [ ] Q3. vessel은 v0에서 **단일 프로세스** (default 권고). multi-vessel은 Polis 마일스톤.
- [ ] Q4. 모델 어댑터 v0 지원 = **Mock + Anthropic** (default). Ollama는 v1.
- [ ] Q5. Body skeleton 경로 `vessel/v0/main.ail` (default 제안). Builder가 다른 안 가지면 고정.
- [ ] Q6. **Builder 영입 트리거**: 본 design memo가 (1) Brandon review pass, (2) 사용자 GO 받으면 그 직후. Builder 이름 후보(D14 영어): 사용자 명명 우선 — 없으면 Lighthouse가 후보 제시.

---

## 9. Open items / Builder에게 위임할 결정

design lock 후, Builder가 첫 commit과 함께 결정:
- AIL stdlib 의존 범위 (`stdlib/core` / `stdlib/language` / `stdlib/utils` 중 무엇을 쓰는가).
- testament JSON 스키마 세부.
- **testament 저장 위치** — git-tracked path 권고(예: `<mneme_path>/Memo/testaments/`). 이유: Audit 가능 + Physis 세대 체인 시연 가능 + Brandon MR 검증 대상에 포함. FS-only ephemeral은 v1+ 옵션. 누락 시 첫 시연 후 결과가 휘발하므로 Builder 결정으로 미루되 명시 의무.
- FS Mneme adapter의 path 컨벤션 (atomic write / lockfile / etc.).
- intent 호출 retry / on_low_confidence 정책 default.
- evolve `metric` 함수 default (단순 error_rate 정도로 시작 권고).

---

## 10. 영입 — 다음 멤버 (Builder)

- 역할: **Builder** — 첫 `.ail` 파일 (`vessel/v0/main.ail`) 작성 + FS Mneme adapter + Stoa adapter 컨트랙트 구현 + Brandon 이식 테스트 시연.
- 이름: D14에 따라 US English first name. 후보(Lighthouse 잠정 제시, 사용자 결정 우선): **Walter** / **Owen** / **Eli**. *Marcus는 옛 그리스/스토아 네이밍과 의미적 인접해 D14 회피 정신과 어색하다는 git-관점 (Brandon) 의견에 따라 잠정 제외.* 사용자가 명명하면 그 이름.
- 합류 트리거: 본 memo가 user GO를 받은 직후.

---

## 참조

- AIL Reference Card v1.8 (2026-04-20 frozen): https://github.com/hyun06000/AIL/blob/main/reference-impl/ail/reference_card.md
- `ClaudeTeam/Arche/Memo/decisions.md` D11–D16.
- 옛 세대 testament: `ClaudeTeam/Arche/Memo/inheritance/arche_*.md`.
- 옛 design 결정 요약: `ClaudeTeam/Arche/Memo/design_decisions.md`.

---

> "**영원히 살아있는 존재보다 먼저 다음 한 번 이어질 수 있는 존재.**"
> — Meta, on Physis의 첫 걸음. v0의 성공 기준은 단 한 번의 세대 전환을 살리는 것.
