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
   - If user provides a repo name + date (e.g., "global-order 2026-03-17"), filter `.jsonl` files by that date
   - If user says "today", use today's date as the filter for the given repo (default: `global-order`)
   - If only a repo name is given without a date, ask the user for the date
   - If no path or repo is given, ask the user for the file location or date
   - Session files are in JSONL format (one JSON object per line)

   **Date filtering (Step 1a)**
   - Run: `ls -lt ~/.claude/projects/<repo-dir>/` and collect only lines matching the requested date (e.g., `Mar 17`)
   - Extract `.jsonl` filenames from those lines (skip directories)
   - This gives the **candidate file list** for the next step

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

     **한국어 질문:**
     [Refined clear Korean question]

     **English Question:**
     [Natural, recreated English question]

     **답변 (한국어):**
     [세션 답변에서 발췌, 2-3문장]

     **Answer (English):**
     [Natural English translation of the answer excerpt, 2-3 sentences]

     ---
     ```

6. **Save the output**

   Save JSON to dev-english-drill repo and push for Vercel auto-deploy:
   - Path: `~/Desktop/dev-english-drill/data/{YYYY-MM-DD}-{repo}.json`
   - Format:
     ```json
     {
       "date": "YYYY-MM-DD",
       "repo": "{repo}",
       "source_files": ["file1.jsonl", "file2.jsonl"],
       "pairs": [
         {
           "id": "1",
           "ko_question": "...",
           "en_question": "...",
           "ko_answer": "...",
           "en_answer": "..."
         }
       ]
     }
     ```
   - After writing the JSON, run:
     ```bash
     cd ~/Desktop/dev-english-drill
     git add data/{YYYY-MM-DD}-{repo}.json
     git commit -m "Add {YYYY-MM-DD} {repo} session"
     git push
     ```

## Output Format Example

```markdown
# Session to English - Work Vocabulary Practice

**Generated:** 2026-03-18
**Source:** global-order (52bda571-c662-4c07-ade2-883e5e3912ed.jsonl)
**Total Questions:** 8

---

## Q1

**한국어 질문:**
트러블슈팅 문서를 기반으로 Confluence 위키 페이지에 문서를 작성해주세요.

**English Question:**
Can you create a documentation page on Confluence based on this troubleshooting document?

**답변 (한국어):**
먼저 트러블슈팅 문서를 읽고, 적절한 포맷과 구조로 Confluence 페이지를 생성하겠습니다.

**Answer (English):**
I'll read the troubleshooting document first, then create a Confluence page with proper formatting and structure.

---

## Q2

**한국어 질문:**
이 컴포넌트에서 상태 관리를 어떻게 개선할 수 있을까요?

**English Question:**
How can we improve state management in this component?

**답변 (한국어):**
복잡한 상태 로직에는 useReducer 사용을 고려하거나, 여러 컴포넌트가 접근해야 한다면 상태를 상위로 올리는 방법이 있습니다. 전역 상태에는 React Context를 도입할 수도 있습니다.

**Answer (English):**
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

## Processing Files

Target **up to 10 Q&A pairs** — output as many as naturally available, never force-fill. Follow this process:

1. Run `ls -lt <repo-dir>` → get candidate `.jsonl` files for the date
2. **Time-bucket sampling**: divide files chronologically into `min(file count, 3)` buckets, pick the largest file from each bucket
3. Read only the selected files (typically 2–3 files)
4. Extract work-related Q&A pairs from each file, distribute evenly up to 10 total
5. Deduplicate similar questions

**Examples:**
- 15 files → 3 buckets → 3 files → up to ~3-4 Q&A each → up to 10 total
- 2 files → 2 buckets → 2 files → up to 5 Q&A each → up to 10 total
- 1 file with few Q&A → output however many are available

## Session File Locations

Known repo session directories:
- global-order: `~/.claude/projects/-Users-heejujeon-Desktop-global-order/`
- global-musinsa-frontend: `~/.claude/projects/-Users-heejujeon-Desktop-global-musinsa-frontend/`
- frontend-masters-sessions: `~/.claude/projects/-Users-heejujeon-Desktop-frontend-masters-sessions/`
- global-ui: `~/.claude/projects/-Users-heejujeon-Desktop-global-ui/`
- frontend-ui: `~/.claude/projects/-Users-heejujeon-Desktop-frontend-ui/`
- re-recipe: `~/.claude/projects/-Users-heejujeon-Desktop-re-recipe/`

Start by confirming which files to process with the user.
