# AI Agent 세션 로그 가이드

## 1. 개요

AI Agent(Claude Code 등)에게 내린 프롬프트와 그로 인해 변경된 파일·출력 결과를 JSON 형식으로 기록하는 시스템이다.

> **주의**: `logs/`는 `.gitignore`에 등록되어 있어 GitHub에 올라가지 않는다.
> 로컬 작업 이력 전용이다.

---

## 2. 디렉토리 구조

```
my-todolist-u/
└── logs/
    └── sessions/
        ├── 2026-03-23T10-00-00.json
        ├── 2026-03-23T14-30-00.json
        └── ...
```

- 파일명 형식: `{ISO8601 timestamp}.json`
- 세션 1회(프롬프트 1개)당 파일 1개

---

## 3. JSON 구조

```json
{
  "timestamp": "2026-03-23T10:00:00Z",
  "prompt": "사용자가 입력한 프롬프트 원문",
  "changes": [
    {
      "file": "변경된 파일 경로",
      "type": "edit | create | delete",
      "summary": "변경 내용 요약"
    }
  ],
  "output": "AI가 출력한 응답 텍스트 (선택)"
}
```

### 필드 설명

| 필드 | 필수 | 설명 |
|------|------|------|
| `timestamp` | ✅ | 프롬프트 입력 시각 (ISO 8601) |
| `prompt` | ✅ | 사용자가 입력한 프롬프트 원문 |
| `changes` | ✅ | 변경된 파일 목록 (없으면 빈 배열 `[]`) |
| `changes[].file` | ✅ | 프로젝트 루트 기준 상대 경로 |
| `changes[].type` | ✅ | `edit` / `create` / `delete` 중 하나 |
| `changes[].summary` | ✅ | 변경 내용 한 줄 요약 |
| `output` | ❌ | AI 응답 중 기록할 가치 있는 내용 |

---

## 4. 저장 과정

```
사용자 프롬프트 입력
        │
        ▼
  AI가 작업 수행
  (파일 수정, 생성, 삭제 등)
        │
        ▼
  작업 완료 후 로그 JSON 생성
  logs/sessions/{timestamp}.json
```

AI가 작업을 마친 뒤 해당 세션의 프롬프트·변경 내역을 JSON으로 직접 작성한다.
파일명의 타임스탬프는 프롬프트를 받은 시각 기준이다.

---

## 5. Claude Code에게 로그 저장 요청하는 방법

작업 요청 시 아래 문구를 함께 입력한다:

```
{작업 내용}
작업이 끝나면 이번 세션을 logs/sessions/{timestamp}.json 에 저장해줘.
```

또는 작업 완료 후 별도로 요청할 수도 있다:

```
방금 작업을 logs/sessions/ 에 JSON으로 저장해줘.
```

---

## 6. 실제 예시

**프롬프트**:
```
playwright_mcp.md에서 .playwright-mcp 디렉토리가 gitignore 처리되어 있음을 명시해줘.
작업이 끝나면 이번 세션을 logs/sessions/{timestamp}.json 에 저장해줘.
```

**생성된 `logs/sessions/2026-03-23T10-00-00.json`**:
```json
{
  "timestamp": "2026-03-23T10:00:00Z",
  "prompt": "playwright_mcp.md에서 .playwright-mcp 디렉토리가 gitignore 처리되어 있음을 명시해줘.",
  "changes": [
    {
      "file": "playwright_mcp.md",
      "type": "edit",
      "summary": "섹션 1·6에 gitignore 주의 문구 추가, 특정 파일명을 {timestamp} 패턴으로 일반화, 디렉토리 구조에 (gitignore) 표시"
    }
  ],
  "output": ""
}
```
