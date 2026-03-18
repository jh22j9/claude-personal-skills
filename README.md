# claude-personal-skills

Claude Code 개인 스킬 모음입니다.

## 설치 방법

### 1. 레포 클론

```bash
cd ~/.claude
git clone git@github.com:jh22j9/claude-personal-skills.git personal-marketplace
```

### 2. settings.json 설정

`~/.claude/settings.json`의 `enabledPlugins`에 원하는 스킬을 추가합니다.

```json
{
  "enabledPlugins": {
    "session-to-english@personal-marketplace": true
  }
}
```

## 동기화

스킬을 수정한 후:
```bash
cd ~/.claude/personal-marketplace
git add .
git commit -m "Update skill"
git push
```

다른 기기에서 최신화:
```bash
cd ~/.claude/personal-marketplace
git pull
```

## 스킬 목록

| 스킬 | 설명 |
|---|---|
| `session-to-english` | 한국어 Claude 세션을 영어 업무 표현 학습 자료로 변환 |
