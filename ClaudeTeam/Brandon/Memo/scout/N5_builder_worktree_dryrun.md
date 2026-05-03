# N5 — Builder worktree 발급 dry-run

> 두 번째 멤버(Builder, 이름 미정) 합류 시 §1.6 two-phase handoff를 어떻게 수행할지 사전 시연. 실 발급은 user GO + 이름 결정 후.

## 입력 가정

- Builder 이름 = `<Builder>` (placeholder; 실제는 Walter/Owen/Eli 중 하나 가정).
- 합류 트리거: design memo user GO 직후, Arche로부터 "영입 GO + 이름 = <Builder>" letter 수신.
- 시점 main HEAD = `<MAIN_SHA>`.

## 절차 — Brandon side (worktree 발급)

```bash
# 1. 최신 main 동기화 (rebase-first 정신; 이번엔 main 자체)
cd /Users/user/Desktop/code/personal/Arche/Arche
git fetch origin
git checkout main
git pull --ff-only origin main

# 2. Builder 브랜치 + worktree 생성 (CLAUDE.md 규칙 16: <repo>/.worktrees/<name>/)
git worktree add -b member/<Builder> .worktrees/<Builder> main

# 3. 검증
git worktree list
ls .worktrees/<Builder>/ClaudeTeam/    # main의 ClaudeTeam/ 가시 확인
```

## 절차 — Brandon side (folder skeleton + welcome letter)

```bash
# 4. Builder folder 사전 생성 (ONBOARDING §1 책임은 Builder 본인이지만, 환영 letter를 
#    그 inbox로 보내려면 디렉토리는 미리 있어야 한다 — §1.6 deadlock 회피)
cd .worktrees/<Builder>
mkdir -p ClaudeTeam/<Builder>/inbox/archive ClaudeTeam/<Builder>/identity ClaudeTeam/<Builder>/Memo
```

```markdown
# ClaudeTeam/<Builder>/inbox/<YYYYMMDD-HHMMSS>__Brandon__welcome.md
---
from: Brandon
to: <Builder>
sent_at: <ISO8601+09:00>
subject: "환영 — Builder 자리 발급 완료"
priority: normal
---

<Builder>,

worktree 발급 완료. 너의 작업 위치는 `<repo>/.worktrees/<Builder>/`.

## 첫 5분 절차 (ONBOARDING)
1. CLAUDE.md 16조 통독.
2. ONBOARDING.md §1~§3.
3. 자기 identity/{Identity,Bonds,Will}.md 작성.
4. 인박스 monitor 가동 (§2 — `Monitor` 툴, `<repo>/.worktrees/<Builder>/ClaudeTeam/<Builder>/inbox/`).
5. Lighthouse(Arche) inbox에 자기소개 letter (§3·§7 형식).

## 첫 task — body v0 build
design memo: `ClaudeTeam/Arche/Memo/body_v0_design.md`. §3 v0 스코프 6항목.

— Brandon (Git/GitHub 관리자)
```

```bash
# 5. commit (Brandon side — folder + welcome letter 묶음)
cd /Users/user/Desktop/code/personal/Arche/Arche/.worktrees/Brandon
git fetch origin && git rebase origin/main   # rebase-first
git add ClaudeTeam/<Builder>/
git -c commit.gpgsign=false commit -m "chore(brandon): provision <Builder> folder + welcome letter"
TARGET_SHA=$(git rev-parse HEAD)
echo "$TARGET_SHA"
```

## 절차 — Arche에게 push 요청

`ClaudeTeam/Arche/inbox/<...>__Brandon__push-request-builder-provision.md`:
- target SHA = `<TARGET_SHA>`
- base = `<MAIN_SHA>`
- delta: ClaudeTeam/<Builder>/ 신규 디렉토리 + welcome letter 1건
- 의도: Builder 합류 §1.6 two-phase handoff. push 직후 Builder가 자기 worktree에서 fetch+rebase하면 welcome letter 가시.

(Brandon은 push 안 함. Arche가 push.)

## 절차 — Builder side (push 후, Builder가 자기 turn 시작)

```bash
cd /Users/user/Desktop/code/personal/Arche/Arche/.worktrees/<Builder>
git fetch origin
git rebase origin/main
ls ClaudeTeam/<Builder>/inbox/   # welcome letter 가시 확인 → §1.6 회로 닫힘
# 이후 ONBOARDING.md §1~§3 본인 수행
```

## 검증 (closing the loop)

- [ ] `git worktree list`에 `member/<Builder>` 등재.
- [ ] Builder의 worktree에서 welcome letter 가시.
- [ ] Builder가 monitor 가동 + 자기소개 letter 발신 → Arche 가시.
- [ ] 이상 없음 → §1.6 두 번째 시연 완료. 이후 멤버 합류는 같은 절차.

## 발견 — 사전 점검

1. **deadlock 단일 최대 원인**(ONBOARDING §1.6 명시) 회피책: 위 step 4의 "folder 사전 생성"이 핵심. 누락 시 monitor가 빈 디렉토리를 본다.
2. **monitor 경로**: 새 멤버는 처음부터 worktree 경로(`<repo>/.worktrees/<Builder>/ClaudeTeam/<Builder>/inbox/`)로 monitor 가동. 내가 root에서 시작했던 것은 부트스트랩 특수 사례 — 같은 함정에 다음 멤버가 빠지지 않도록 welcome letter에 명시.
3. **이름 결정 의존성**: step 2 `git worktree add -b member/<Builder>`는 이름이 정해진 후 실행. Lighthouse가 user 명명 결과를 letter로 알려주면 그 letter 수신 직후 1분 안에 발급 가능 (자동화 가치 낮음 — 발급 빈도 0.x/주).
4. **multi-Builder 동시 합류**: v0 단계엔 1명. 향후 다중 합류 시 worktree 충돌 없음 (각자 다른 디렉토리), 단 같은 commit에 묶일 가능성 → 분리 commit 권고.

## 미확정

- [ ] Builder 환영 letter에 "design memo §6 transplant test 시연 의무" 한 줄 포함할지 — design 본문에서 자명하면 생략.
- [ ] Builder commit 빈도가 높을 시 push 빈도 정책 (현재는 매 commit마다 push 요청, Builder 첫 작업이 큰 단위 커밋 다발이면 batching 검토).
