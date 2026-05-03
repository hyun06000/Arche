# N2 — `.ail` 확장자 처리 정찰

> Git/GitHub이 `.ail` 파일을 어떻게 다룰지의 표면 정찰. 도입 결정은 design 후.

## 후보 메커니즘

### (1) `.gitattributes` — 텍스트/이진 + EOL + diff driver

가장 기본. repo 루트에 `.gitattributes`를 둔다:

```gitattributes
*.ail text eol=lf
*.ail diff=ail
```

- `text eol=lf`: 줄끝을 LF로 정규화 — Windows 멤버 합류 시 CRLF 오염 방지.
- `diff=ail`: 사용자 지정 diff driver를 묻는 hint. 구현은 `git config diff.ail.xfuncname '<regex>'`로 함수/블록 헤더 인식 패턴을 등록 — `git diff` hunk 헤더에 AIL 함수명이 표시된다. (regex는 AIL 문법 정해진 후 작성.)

### (2) GitHub Linguist — 언어 통계 + syntax highlight

GitHub의 Linguist는 확장자 → 언어를 자체 데이터베이스에서 결정. `.ail`은 등록되어 있지 않아 GitHub는 자동 인식 못함. 두 단계 옵션:

- **단기**: `.gitattributes`에 `*.ail linguist-language=Lisp`(또는 가장 가까운 기존 언어) — GitHub의 코드 통계 그래프에 잡히고 가벼운 highlight를 우회 적용.
- **장기**: linguist 본 저장소(`github/linguist`)에 PR — AIL을 정식 언어로 등재. 단 등재 조건이 있다(공개 코드 샘플 200+, 최소 사용 사례 등). repo가 충분히 자라기 전엔 비현실적.

### (3) `.gitattributes` — merge driver / export 제외

- `*.ail merge=union` 등은 위험 — 코드는 union merge 부적합 (의미 깨짐). 적용 비추.
- `*.ail export-ignore`도 부적합 — `.ail`은 핵심 자산이라 archive에 포함되어야 함.

### (4) 향후 옵션 — custom hash / LFS

`.ail` 파일이 거대해지거나 binary blob을 포함하면 LFS 후보. 현재는 pure text 가정 → LFS 불필요.

## 권고 (단계적)

1. **즉시 도입 가능 (low-risk)**: `.gitattributes` 1줄 — `*.ail text eol=lf`. EOL 정규화는 멤버 OS 다양화에 대비. 첫 `.ail` 파일 들어오기 전에 미리 두는 게 깔끔.
2. **diff driver**: AIL 문법 정해진 후 `xfuncname` regex 등록. design 메모에 "함수/블록 정의 문법" 항목이 잡히면 이 시점에 추가.
3. **Linguist hint**: 첫 `.ail` PR이 들어와 GitHub UI에서 "Plain text"로 잡히는 게 거슬리면 그때 `linguist-language=...` 추가. 현재는 보류.

## 결정 대기 항목

- [ ] (D??) `.gitattributes` 신설 + `*.ail text eol=lf` 1줄 — 첫 substantive MR 시연 후보로 사용 가능.
- [ ] AIL 문법 정해지면 diff `xfuncname` 패턴 작성.
- [ ] Linguist 등재 — 장기 백로그.

## 종속 — N3에 영향

`text eol=lf` 정규화 안 되어 있으면 N3 hook(특히 parse_check)이 EOL 차이로 우연한 실패 가능. N3보다 N2가 먼저.
