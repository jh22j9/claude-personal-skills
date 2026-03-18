# session-to-english

한국어 Claude 세션을 영어 업무 표현 학습 자료로 변환하는 스킬입니다.

## 사용법

Claude Code에서 자연어로 입력하면 자동으로 스킬이 활성화됩니다.

```
session-to-english global-order 2026-03-17
```

### 입력 형식

```
session-to-english <레포명> <날짜> [파일 선택 옵션]
```

| 파라미터 | 설명 | 예시 |
|---|---|---|
| 레포명 | 세션 파일이 있는 레포 | `global-order` |
| 날짜 | 처리할 날짜 (`YYYY-MM-DD` 또는 `today`) | `2026-03-17` |

항상 **10개의 Q&A**를 출력합니다. 별도 옵션 없이 자동으로 시간대별 파일 샘플링을 적용합니다.

- 파일 15개 → 오전/오후/저녁 3구간 → 3파일만 읽기 → 각 파일에서 3~4개
- 파일 2개 → 2구간 → 2파일 읽기 → 각 파일에서 5개
- 파일 1개 → 그 파일에서 10개

### 입력 예시

```
session-to-english global-order 2026-03-17
session-to-english global-order 2026-03-17 random 5
session-to-english global-order today max 3
```

## 출력 형식

각 Q&A는 4개 항목으로 구성됩니다.

```markdown
## Q1

**한국어 질문 (정제):**
트러블슈팅 문서를 기반으로 Confluence 위키 페이지를 작성해주세요.

**English Question:**
Can you create a Confluence page based on this troubleshooting document?

**답변 (한국어):**
먼저 트러블슈팅 문서를 읽고, 적절한 포맷과 구조로 Confluence 페이지를 생성하겠습니다.

**Answer (English):**
I'll read the troubleshooting document first, then create a Confluence page with proper formatting and structure.
```

결과 파일은 `~/Desktop/session-to-english-<레포명>-<날짜>.md`로 저장됩니다.

## 지원 레포

| 레포명 | 경로 |
|---|---|
| `global-order` | `~/.claude/projects/-Users-heejujeon-Desktop-global-order/` |
| `global-musinsa-frontend` | `~/.claude/projects/-Users-heejujeon-Desktop-global-musinsa-frontend/` |
| `frontend-masters-sessions` | `~/.claude/projects/-Users-heejujeon-Desktop-frontend-masters-sessions/` |
| `global-ui` | `~/.claude/projects/-Users-heejujeon-Desktop-global-ui/` |
| `frontend-ui` | `~/.claude/projects/-Users-heejujeon-Desktop-frontend-ui/` |
| `re-recipe` | `~/.claude/projects/-Users-heejujeon-Desktop-re-recipe/` |
