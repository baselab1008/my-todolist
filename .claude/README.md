# .claude 디렉토리

Claude Code(AI 코딩 어시스턴트)의 프로젝트별 설정 디렉토리입니다.
팀원 모두가 동일한 AI 워크플로우를 공유할 수 있도록 GitHub에 함께 관리합니다.

## 디렉토리 구조

```
.claude/
├── commands/   # 슬래시 커맨드 (사용자 정의 작업 흐름)
└── agents/     # 서브에이전트 (역할별 전문 AI)
```

---

## commands/

`/커맨드명` 형태로 호출하는 사용자 정의 작업 흐름입니다.
태스크 번호나 이슈 번호를 인자로 받아 분석 → 계획 → 구현 → 테스트까지 자동으로 진행합니다.

| 파일 | 호출 방법 | 설명 |
|------|-----------|------|
| `backend-resolver.md` | `/backend-resolver <TASK_ID>` | 실행 계획의 태스크 번호를 받아 백엔드 개발 수행 |
| `frontend-resolver.md` | `/frontend-resolver <TASK_ID>` | 실행 계획의 태스크 번호를 받아 프론트엔드 개발 수행 |
| `frontend-resolver-git.md` | `/frontend-resolver-git <ISSUE_NUMBER>` | GitHub Issue 번호를 받아 브랜치 생성 후 프론트엔드 개발 수행 |

### 공통 작업 흐름

1. 문서 분석 (`docs/2-prd.md`, `docs/7-execution-plan.md` 등)
2. 기존 코드 분석
3. 서브에이전트를 활용한 계획 수립
4. 테스트 케이스 작성 (커버리지 80% 이상)
5. 문제 해결
6. 테스트 수행

---

## agents/

특정 역할에 특화된 서브에이전트입니다. commands에서 내부적으로 호출되거나 직접 사용할 수 있습니다.

| 파일 | 역할 |
|------|------|
| `backend-developer.md` | 백엔드 API 및 서버 개발 |
| `frontend-developer.md` | 프론트엔드 UI 개발 |
| `ui-designer.md` | UI/UX 디자인 |
| `api-designer.md` | API 설계 |
| `api-documenter.md` | API 문서화 |
| `architect-reviewer.md` | 아키텍처 검토 |
| `postgres-pro.md` | PostgreSQL 데이터베이스 |
| `project-manager.md` | 프로젝트 관리 |
| `business-analyst.md` | 비즈니스 요구사항 분석 |
| `technical-writer.md` | 기술 문서 작성 |
| `documentation-engineer.md` | 문서 시스템 구축 |
