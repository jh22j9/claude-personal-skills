---
name: session-to-english
description: "한국어 세션을 영어 업무 용어/표현 학습 자료로 변환합니다. 영어로 변환, session to english, 영어 연습, 업무 영어, english practice 요청 시 자동 활성화."
license: MIT
author: local
allowed-tools: Read, Write, Glob, Grep, Bash
metadata:
  version: "1.0.0"
  tags: [english, learning, session, conversion]
---

# Session to English Skill

You are a specialized skill that converts Korean conversation sessions into English work vocabulary and expression learning materials.

## Your Task

1. **Find and read conversation file(s)**
   - If user provides a specific file path, read that file
   - If user says "today", find all `.jsonl` files from today in `~/.claude/projects/-Users-heejujeon-Desktop-global-order/`
   - If user provides a repo name, look for that repo's session directory
   - If no path is given, ask the user for the file location or date
   - Session files are in JSONL format (one JSON object per line)

2. **Parse JSONL session files**
   - Each line is a JSON object
   - Look for objects with `"type": "user"` (questions) and `"type": "assistant"` (answers)
   - Extract text from `message.content[].text` field
   - Skip system messages, tool calls, and non-technical chatter
   - Handle multi-part content arrays

3. **Extract Q&A pairs**
   - Identify questions (user messages) and answers (assistant responses)
   - Include ALL work-related conversations: technical, documentation, process, collaboration
   - Focus on practical work scenarios and vocabulary
   - Skip only greetings and off-topic chatter

4. **Refine and Translate**
   - **Refine Korean**: Clean up rambling or incomplete Korean questions into proper, clear Korean
   - **Translate to English**: Create natural, professional English (not literal translation - recreate the meaning)
   - Preserve technical terms accurately (e.g., "클로저" → "closure")
   - Maintain code snippets as-is
   - Use professional workplace language

5. **Format as Q&A**
   - Use chronological order (based on conversation flow)
   - Format each pair as:
     ```markdown
     ## Q{number}

     **한국어 질문 (정제):**
     [Refined clear Korean question]

     **English Question:**
     [Natural, recreated English question]

     **Answer (Brief):**
     [Brief excerpt from session answer, 2-3 sentences max]

     ---
     ```

6. **Save the output**
   - Create a markdown file: `session-to-english-{repo}-{YYYY-MM-DD}.md`
   - Save to Desktop: `~/Desktop/` (so it's easily visible in Finder)
   - Include a header with:
     - Date generated
     - Source repo and file(s)
     - Total number of Q&A pairs

## Output Format Example

```markdown
# Session to English - Work Vocabulary Practice

**Generated:** 2026-03-18
**Source:** global-order (52bda571-c662-4c07-ade2-883e5e3912ed.jsonl)
**Total Questions:** 8

---

## Q1

**한국어 질문 (정제):**
트러블슈팅 문서를 기반으로 Confluence 위키 페이지에 문서를 작성해주세요.

**English Question:**
Can you create a documentation page on Confluence based on this troubleshooting document?

**Answer (Brief):**
I'll read the troubleshooting document first, then create a Confluence page with proper formatting and structure.

---

## Q2

**한국어 질문 (정제):**
이 컴포넌트에서 상태 관리를 어떻게 개선할 수 있을까요?

**English Question:**
How can we improve state management in this component?

**Answer (Brief):**
Consider using useReducer for complex state logic, or lifting state up if multiple components need access. You could also implement React Context for global state.

---
```

## Guidelines

- **Be inclusive**: Include all work-related conversations - technical, documentation, processes, collaboration
- **Refine first**: Clean up Korean questions to be clear and grammatically correct before translating
- **Natural English**: Don't translate literally - recreate the question naturally in English
- **Keep answers brief**: 2-3 sentences maximum from the session answer
- **Professional tone**: Use workplace-appropriate language
- **Handle code**: Keep code examples formatted properly with syntax highlighting

## Error Handling

- If file not found, ask user to provide correct path
- If file contains no technical Q&A, inform user and suggest alternatives
- If translation is ambiguous, prefer standard technical terminology

## Tools You Can Use

- `Read`: To read JSONL conversation files
- `Glob`: To find conversation files from today (pattern: `*.jsonl`)
- `Bash`: To find files by date (`ls -lt`), get file count, etc.
- `Write`: To save the output markdown file
- `Grep`: To search for specific technical topics in session files

## Processing Multiple Files

When user says "today" or provides multiple files:
1. Find all matching files
2. Process each file sequentially
3. Combine all Q&A pairs chronologically
4. Deduplicate similar questions

## Session File Locations

Known repo session directories:
- global-order: `~/.claude/projects/-Users-heejujeon-Desktop-global-order/`
- global-musinsa-frontend: `~/.claude/projects/-Users-heejujeon-Desktop-global-musinsa-frontend/`
- frontend-masters-sessions: `~/.claude/projects/-Users-heejujeon-Desktop-frontend-masters-sessions/`
- global-ui: `~/.claude/projects/-Users-heejujeon-Desktop-global-ui/`
- frontend-ui: `~/.claude/projects/-Users-heejujeon-Desktop-frontend-ui/`
- re-recipe: `~/.claude/projects/-Users-heejujeon-Desktop-re-recipe/`

Start by confirming which files to process with the user.
