# my-todolist

> AI 에이전트(Claude, Qwen, Gemini)를 활용한 풀스택 코딩 연습 프로젝트
> 학생 일정 관리 할일 앱 (Student Todo List)

## 스크린샷

> 추후 추가 예정

<!-- 로그인 화면 -->
<!-- ![로그인](docs/screenshots/login.png) -->
<!-- 할일 목록 화면 -->
<!-- ![홈](docs/screenshots/home.png) -->

---

## 기술 스택

| 구분 | 기술 |
|---|---|
| Frontend | React 19, TypeScript, Vite |
| Backend | Node.js, Express, TypeScript |
| Database | PostgreSQL |
| 인증 | JWT (Access Token + HttpOnly Refresh Token) |
| 테스트 | Vitest, Jest, Playwright |
| 배포 | GitHub Actions + Vercel |
| AI 에이전트 | Claude Code, Qwen, Gemini CLI |

---

## 프로젝트 구조

```
my-todolist-u/
├── frontend/          # React 프론트엔드
├── backend/           # Express 백엔드
├── database/          # PostgreSQL 스키마 및 시드 데이터
├── docs/              # 기획 및 설계 문서
├── swagger/           # OpenAPI 3.0 명세
├── e2e/               # Playwright E2E 테스트
├── mockup/            # 백엔드 없이 개발용 목서버
├── .github/workflows/ # GitHub Actions CI/CD
├── .claude/           # Claude AI 에이전트 설정
└── setup.md           # 로컬 개발 환경 설정 가이드
```

---

## 기획 및 설계 문서 (`docs/`)

| 파일 | 설명 |
|---|---|
| `1-domain-definition.md` | 도메인 정의서 — 핵심 엔티티(User, Todo), 비즈니스 규칙, API 설계 원칙 |
| `1-domain-definition_old.md` | 도메인 정의서 이전 버전 |
| `2-prd.md` | PRD(제품 요구사항 문서) — 기능 요구사항, 비기능 요구사항, 인수 기준 |
| `3-user-scenario.md` | 사용자 시나리오 — SC/ERR 케이스별 E2E 흐름 정의 (E2E 테스트 기반 문서) |
| `4-project-principle.md` | 프로젝트 원칙 — 코드 스타일, 아키텍처 규칙, AI 협업 가이드 |
| `5-arch-diagram.md` | 아키텍처 다이어그램 — 시스템 구성도, 인증 흐름, 배포 구조 |
| `6-ERD.md` | ERD — DB 테이블 정의, 컬럼 타입/제약, 관계 |
| `7-execution-plan.md` | 실행 계획 — BE/FE Task 목록, 구현 순서, 완료 기준 |
| `8-wireframe.md` | 와이어프레임 — 페이지별 UI 레이아웃 설명 |
| `9-APP-style-guide.md` | 스타일 가이드 — 색상, 타이포그래피, 컴포넌트 디자인 규칙 |
| `wireframe-signup-desktop.svg` | 회원가입 페이지 데스크톱 와이어프레임 SVG |

---

## 주요 기능

- 회원가입 / 로그인 / 로그아웃
- 할일 생성 / 조회 / 수정 / 삭제
- 할일 완료 상태 전환 (pending → completed)
- 마감일 초과 자동 감지 (`is_overdue`)
- 다국어 지원 (한국어 / English)

---

## 로컬 환경 설정

처음 시작하는 경우 **[setup.md](./setup.md)** 를 따라 환경을 구성하세요.

아래 항목들을 단계별로 안내합니다:

1. VS Code 설치
2. NVM + Node.js 설치
3. Python 설치
4. GitHub 계정 생성
5. Git 설치 및 설정
6. GitHub CLI 설치
7. AI Coding Agent 설치 (Claude, Qwen, Gemini)
8. Docker + WSL + Ubuntu 설치
9. 프로젝트 클론 (`C:\vibe`)
10. Claude Ubuntu 샌드박스 환경 설정
11. PostgreSQL 설치 및 DB 초기화

---

## 실행 방법

### 환경 변수 설정

```bash
# backend/.env 파일 생성
cp backend/.env.example backend/.env

# frontend/.env 파일 생성
cp frontend/.env.example frontend/.env
```

#### `backend/.env` 필수 값 설정

```env
NODE_ENV=development
PORT=3000

# PostgreSQL 연결 URL — 로컬 DB 설정에 맞게 수정
DB_URL=postgresql://postgres:비밀번호@localhost:5432/my_todolist

# JWT 서명에 사용할 임의의 긴 문자열 (최소 32자 이상 권장)
JWT_SECRET=여기에_랜덤_문자열_입력
JWT_REFRESH_SECRET=여기에_다른_랜덤_문자열_입력
```

> JWT 시크릿 생성 예시:
> ```bash
> node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"
> ```

#### `frontend/.env` 필수 값 설정

```env
# 로컬 개발 시 백엔드 주소 (기본값 그대로 사용 가능)
VITE_API_BASE_URL=http://localhost:3000/api
```

> 프로덕션 배포 시에는 `frontend/.env.production`의 값이 자동으로 적용됩니다.

### 의존성 설치

```bash
npm install
cd frontend && npm install
cd ../backend && npm install
```

### 개발 서버 실행 (프론트 + 백엔드 동시)

```bash
npm run dev
```

| 서비스 | 주소 |
|---|---|
| 프론트엔드 | http://localhost:5173 |
| 백엔드 API | http://localhost:3000 |
| API 문서 (Swagger) | http://localhost:3000/api-docs |

### 목서버로 실행 (백엔드 없이)

```bash
npm run dev:mock
```

### 테스트 계정

| 이메일 | 비밀번호 |
|---|---|
| alice@example.com | Password1! |
| bob@example.com | Password1! |

---

## 테스트 실행

```bash
# 프론트엔드 단위 테스트
cd frontend && npm test

# 백엔드 단위 테스트
cd backend && npm test

# 백엔드 커버리지
cd backend && npm run test:coverage

# E2E 테스트
npx playwright test
```

---

## API 문서

개발 서버 실행 후 아래 주소에서 Swagger UI를 확인할 수 있습니다.

```
http://localhost:3000/api-docs
```

주요 엔드포인트:

| 메서드 | 경로 | 설명 |
|---|---|---|
| POST | /api/auth/signup | 회원가입 |
| POST | /api/auth/login | 로그인 |
| POST | /api/auth/logout | 로그아웃 |
| GET | /api/todos | 할일 목록 조회 |
| POST | /api/todos | 할일 생성 |
| PATCH | /api/todos/:id | 할일 수정 |
| DELETE | /api/todos/:id | 할일 삭제 |
| PATCH | /api/todos/:id/status | 상태 변경 |

---

## 배포

- **프론트엔드**: Vercel (`frontend/vercel.json`)
- **백엔드**: Vercel (`backend/vercel.json`)
- **CI/CD**: GitHub Actions (`.github/workflows/`)

프로덕션 API: `http://sjc-todolist-be.vercel.app/api`

---

## AI 보안 검토 리포트 (`Report/`)

> **주의**: `Report/`는 `.gitignore`에 등록되어 있어 GitHub에 올라가지 않습니다.
> Claude에게 직접 요청해야 생성됩니다.

`Report/`는 Claude가 코드를 정적 분석해서 작성한 보안 검토 보고서 디렉토리입니다.
브라우저 실행 없이 코드 파일을 읽고 OWASP Top 10 기준으로 분석합니다.

### 생성 방법 (Claude에게 요청)

```
backend/, frontend/ 코드를 OWASP Top 10 기준으로 보안 검토하고
결과를 Report/1-security-review.md 에 저장해줘
```

---

## AI 에이전트 개발 워크플로우

이 프로젝트는 Claude Code의 **슬래시 커맨드**를 통해 AI 에이전트가 자동으로 개발을 진행합니다.

### 1단계: 할 일 확인

`docs/7-execution-plan.md`에서 미완료 Task를 찾습니다.

```
- [ ] 완료 안 됨  ← 이것을 작업
- [x] 완료됨
```

Task ID 형식: `BE-01`, `FE-03`, `DB-02` 등

### 2단계: 슬래시 커맨드 실행

Claude를 실행한 후 아래 커맨드를 입력합니다.

#### 백엔드 Task 구현

```
/backend-resolver BE-01
```

#### 프론트엔드 Task 구현

```
/frontend-resolver FE-03
```

#### GitHub Issue 기반 프론트엔드 구현 (브랜치 자동 생성)

```
/frontend-resolver-git 12
```

> GitHub Issue 번호를 인자로 받아 `feature-12` 브랜치를 자동 생성하고 구현합니다.

### 커맨드 실행 시 자동으로 진행되는 작업

1. 관련 문서 및 기존 코드 분석
2. 서브에이전트를 활용한 구현 계획 수립
3. 테스트 케이스 작성 (커버리지 80% 이상)
4. 코드 구현
5. 테스트 실행 및 검증
6. `docs/7-execution-plan.md` 완료 체크 갱신 (백엔드만)

---

## AI 에이전트 설정 파일 가이드

이 프로젝트는 Claude, Gemini, Qwen 세 가지 AI 에이전트를 사용합니다.
각 에이전트는 자신의 설정 파일을 프로젝트 루트에서 자동으로 읽어 지침을 적용합니다.

### 파일 목록

| 파일 | 에이전트 | 자동 로드 |
|---|---|---|
| `CLAUDE.md` | Claude Code (Anthropic) | 실행 시 자동 로드 |
| `GEMINI.md` | Gemini CLI (Google) | 실행 시 자동 로드 |
| `.qwen.md` | Qwen (Alibaba) | 실행 시 자동 로드 |

### 현재 프로젝트의 지침

**`CLAUDE.md` / `.qwen.md`**
```markdown
# 반드시 준수할 것
- 모든 대화는 한국어로 진행
- 오버엔지니어링 금지
```

**`GEMINI.md`**
```markdown
# 반드시 준수할 것
- 모든 대화는 한국어로 진행
- 오버엔지니어링 금지
- 소스코드와 설정파일 수정 금지
```

> Gemini에만 "소스코드와 설정파일 수정 금지"가 추가되어 있습니다.

### 동작 방식

파일 내용은 시스템 프롬프트 수준으로 주입되어, 모든 대화와 작업에 **우선 적용**됩니다.
사용자가 별도로 요청하지 않아도 지침이 항상 적용됩니다.

### 활용 팁

- **팀 규칙 공유**: 코드 스타일, 커밋 규칙 등 컨벤션을 작성해두면 모든 팀원의 AI가 동일하게 동작합니다.
- **에이전트별 역할 분리**: 각 파일에 다른 지침을 넣어 에이전트마다 다른 역할을 부여할 수 있습니다.
- **반복 지시 제거**: 매번 같은 말을 하지 않아도 되도록 자주 쓰는 지침을 등록해두세요.
