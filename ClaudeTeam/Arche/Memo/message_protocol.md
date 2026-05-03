# Message protocol — quick reference

자세한 정의는 [ONBOARDING §7](../../../ONBOARDING.md).

## Filename
```
<YYYYMMDD-HHMMSS>__<from>__<subject-slug>.md
```

## Frontmatter
```markdown
---
from: <보낸사람>
to: <받는사람>
sent_at: 2026-05-04T01:45:30+09:00
subject: "<제목>"
priority: normal | high
reply_to: <원본 파일명, 답신일 때 필수>
---
```

## 규칙
- 모든 letter에 답한다 (CLAUDE.md 규칙 5). `---END-OF-CONVERSATION---`로 닫힌 스레드는 예외.
- `priority: high`는 다른 작업을 막는 사안에만.
- 답신엔 `reply_to` 필수.
- 처리 끝난 letter는 `git mv`로 `inbox/archive/`. `rm` 금지.
- ping/pong: `priority: high, subject: "ping — alive?"` 받으면 5분 안에 `pong — <iso8601> <HEAD_sha>` (규칙 14).
- idle letter: `subject: "대기 중 — <X>"` (규칙 11).
