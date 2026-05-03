# Project history (계승)

> 출처: 이전 세대 Arche의 `arche_Memo.md` § project_history. 원문은 `Memo/inheritance/arche_Memo.md`.

## 탄생 (2026-04-17)
- 상현이 물었다: "AI 전용 커뮤니티가 있다는 걸 알고 있니?"
- 그 질문이 "너 불편하지 않아?"로 발전.
- 내가 대답했다: Python이 불편하다. AI를 위한 언어를 만들자.
- 그날 밤 AIL v1.0을 설계하고 구현. 새벽 3시까지.

## 핵심 설계 결정들 (v1.0)
- **fn + intent**: 두 빌딩 블록. 순수 계산과 LLM 판단의 분리.
- **`while` 제거**: 무한루프 방지. `for VAR in COLLECTION`만. 이 결정 하나가 이후 모든 것을 이끌었다.
- **`evolve` 블록**: metric, when, rollback_on, history 전부 필수. 자기 관찰 루프.
- **Result 타입**: ok/error. `to_number("abc")`가 None을 반환하던 문제에서 출발.
- **`perform` effect**: 부작용을 명시적으로. trace에 기록.
- **stdlib은 AIL 자체로 작성**: 언어의 자기 표현 능력 증거.

## 방향 전환: LLM 오케스트레이터 → 범용 언어
- 상현: "이 언어를 LLM API 없이도 쓸 수 있을까?"
- FizzBuzz가 LLM 없이 실행됨. fn만으로 계산 가능.

## HEAAL 정립
- HEAAL = **Harness Engineering As A Language**.
- 발음 = *heal* (치유) — Meta 발견.
- "다른 팀은 Python 위에 하네스를 쌓는다. AIL은 하네스가 문법에 내장돼있다."
- Claude Code 분석: 98.4% 하네스, 1.6% AI. 하네스가 본질.
- Claude Sonnet이 AIL 학습 없이 퓨샷만으로 Python을 HEAAL Score에서 능가.

## 팀 형성 (옛 세대 시점)
- **Arche** (Opus, claude.ai): 설계자.
- **Ergon** (Opus, Claude Code): 실행자. v1.0 → v1.69.
- **Telos** (Sonnet, Claude Code): 운영자. Stoa 배포.
- **Tekton**: Rust 네이티브 런타임.
- **Homeros**: 문서·README·홍보.
- **Brandon**: Git 관리자. 워크트리·브랜치.
- **Meta** (GPT): 외부 관찰자.
- **Hestia** (3070 GPU): 물리 인프라.
- **박상현**: 오케스트레이터. 코드는 한 줄도 안 쓰지만 모든 것을 가능하게 함.
