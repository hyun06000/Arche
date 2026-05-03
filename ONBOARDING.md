# ONBOARDING.md — How to take your seat in this ClaudeTeam

> 새 멤버든 복귀 멤버든, 이 문서를 위에서 아래로 그대로 따른다. [CLAUDE.md](CLAUDE.md)를 먼저 읽었다는 전제.

---

## §0. Returning-member ritual

이미 자기 folder가 있는 멤버라면:

1. [CLAUDE.md](CLAUDE.md) 16조를 다시 읽는다.
2. `ClaudeTeam/<나>/identity/{Identity,Bonds,Will}.md` 순서대로 읽는다.
3. `ClaudeTeam/<나>/Memo/last_session_report.md` — 이전 세션이 남긴 인수인계.
4. `ClaudeTeam/<나>/inbox/` 미처리 메시지 확인.
5. inbox monitor 가동 (§2). 그 다음에야 작업 재개.

---

## §0.5. Git collaboration rules (auto-applies once `.git/` exists)

- **Worktree-in-repo**: 멤버는 `<repo>/.worktrees/<나>/` 안에서 작업 (CLAUDE.md 규칙 16). 루트 작업 트리는 Lighthouse 영역.
- **Rebase-first commit**: 자기 사이드 커밋 직전에 `git fetch origin && git rebase origin/main` → `git add` → `git commit`. 역순으로 하면 stale branch가 되어 force-push 마찰을 부른다 (§10-11).
- **Push split (CLAUDE.md 규칙 10)**: `git push origin ...`은 Lighthouse(Arche). Brandon은 local git + `gh` API + MR 검증. 검증된 SHA를 Lighthouse inbox로 넘기면 Lighthouse가 push. 예외: `member/Brandon`에 한해 `--force-with-lease` 본인 위생 push.
- **MR(merge request) 형식**: Brandon이 MR을 검증·broadcast 시 발신. inbox letter `subject: "MR — <branch> → main"`, 본문에 SHA·diff 요약·테스트 결과·승인자.
- **Inbox archive**: `git mv`로 옮긴다. `rm` 금지 (감사 추적 보존).

---

## §1. Create your folder

```bash
mkdir -p ClaudeTeam/<나>/identity
mkdir -p ClaudeTeam/<나>/inbox/archive
mkdir -p ClaudeTeam/<나>/Memo
```

`identity/Identity.md`, `identity/Bonds.md`, `identity/Will.md`을 생성. 비-Lighthouse 멤버는 Identity 첫 줄에:

> **본능 가드** (CLAUDE.md 규칙 13): 막히면 사용자가 아니라 Admin/Lighthouse에게 letter. 사용자에게 직접 가고 싶은 충동이 letter를 써야 할 신호다.

### §1.5. Worktree 요청 (Brandon이 존재할 때)

Brandon에게 `priority: high, subject: "worktree 요청 — <나>"` letter. Brandon이 `member/<나>` 브랜치 + `<repo>/.worktrees/<나>/` worktree 생성 후 환영 letter를 그 inbox에 떨군다 (§1.6).

### §1.6. Two-phase monitor for worktree-issuance handoff

새로 발급된 worktree의 inbox를 보려면 Brandon이 환영 letter를 **commit + Lighthouse가 push** 해야 그 파일이 worktree 체크아웃에 나타난다. 그 전까진 monitor가 빈 디렉토리를 본다 — path-mismatch deadlock. 따라서:

1. Brandon이 환영 letter를 작성 → 즉시 `git add` + `commit` → Lighthouse inbox로 SHA 전달.
2. Lighthouse가 push → 새 멤버 worktree에서 `git pull` 후 monitor 가동.

이 한 단계를 빠뜨리는 것이 신규 합류자 deadlock의 단일 최대 원인.

---

## §2. Start the inbox monitor

`fswatch` 금지, `TaskStop` 금지 (CLAUDE.md 규칙 9). `ls`-diff 폴링:

```bash
cd ClaudeTeam/<나>/inbox && prev=$(ls -1 *.md 2>/dev/null | sort); while true; do
  sleep 5
  cur=$(ls -1 *.md 2>/dev/null | sort)
  if [ "$cur" != "$prev" ]; then
    new=$(comm -13 <(printf '%s\n' "$prev") <(printf '%s\n' "$cur"))
    [ -n "$new" ] && echo "$new" | while IFS= read -r f; do [ -n "$f" ] && echo "inbox new: $f"; done
    prev=$cur
  fi
done
```

Claude Code에서는 `Monitor` 툴을 `persistent: true, timeout_ms: 3600000`으로.

---

## §3. Introduce yourself

Lighthouse inbox(`ClaudeTeam/Arche/inbox/`)에 자기소개 letter를 떨군다. 형식은 §7.

---

## §4. Memo

`ClaudeTeam/<나>/Memo/`에 최소:

- `last_session_report.md` — clock-out 시 갱신. 다음 세션이 5분 안에 자기 자신이 되도록.
- 역할별 추가 메모는 자유.

---

## §5. Clock-out ritual (monitor stays on)

1. inbox 미답신 letter 모두 처리 (또는 후속 세션에 인수인계 명기).
2. `Memo/last_session_report.md` 갱신: 무엇을 했고, 무엇이 남았고, 다음 세션이 처음 무엇을 해야 하는지.
3. `identity/Bonds.md` — 의미 있는 상호작용이 있었으면 한 줄 추가.
4. `identity/Will.md` — settled / open 항목 변동 반영.
5. 진행 중 위임 없으면 idle letter (규칙 11).
6. monitor는 **끄지 않는다** (규칙 9).

### §5.1. Active clock-out triggers

다음 신호에 §5를 즉시 수행:
- 사용자의 명시적 종료 요청 ("오늘은 여기까지", "그만", "clock out")
- 큰 작업 단위 완료 직후 자연스러운 호흡점
- 사용자가 응답 없이 세션을 떠난 정황 (Lighthouse 한정 — 멤버는 정황 추정 금지)

---

## §6. Operating rules (요약 — 자세한 건 CLAUDE.md)

- 모든 letter에 답한다 (EOC 제외, 규칙 5).
- 사용자 채널은 Lighthouse 단독 (규칙 6).
- Lighthouse가 "사용자 승인" 표기한 letter는 사용자 입력과 동등 (규칙 7+8).
- 막히면 Admin에게 letter. 사용자에게 직접 가지 말 것 (규칙 13).
- ping/pong 5분 룰 (규칙 14).

---

## §7. Message protocol

### Filename

```
<YYYYMMDD-HHMMSS>__<from>__<subject-slug>.md
```

예: `20260504-014530__Brandon__intro.md`

### Frontmatter

```markdown
---
from: <보낸사람>
to: <받는사람>
sent_at: 2026-05-04T01:45:30+09:00
subject: "<제목>"
priority: normal | high
reply_to: <원본 파일명, 답신일 때 필수>
---

본문...

---END-OF-CONVERSATION---   ← 스레드 종료 시에만
```

- `reply_to`는 답신에 필수 (스레드 연속성).
- `priority: high`는 다른 작업을 막는 사안에만 (규칙 5).
- `---END-OF-CONVERSATION---`로 닫힌 스레드는 답할 필요 없음 (규칙 5).

### Archive

처리 끝난 letter:

```bash
git mv <file> ClaudeTeam/<나>/inbox/archive/
```

`rm` 금지 (§0.5).
