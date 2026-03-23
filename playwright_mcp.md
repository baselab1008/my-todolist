# Playwright & Playwright MCP 완전 가이드

## 1. 관련 파일 전체 목록

### 설정 파일

| 파일 | 설명 |
|------|------|
| `playwright.config.ts` | Playwright 전역 설정 (baseURL, 타임아웃, 브라우저, 스크린샷 정책 등) |
| `package.json` (루트) | `@playwright/test` 의존성 (`^1.58.2`) 선언 |

### E2E 테스트 스펙

| 파일 | 설명 |
|------|------|
| `e2e/INT-01-auth-flow.spec.ts` | 인증 전체 흐름 E2E (회원가입 → 로그인 → 토큰 갱신 → 로그아웃 → 보안 검증) |
| `e2e/login-failure.spec.ts` | 로그인 실패 시나리오 7가지 (한국어 UI 기준, 스크린샷 직접 저장) |
| `e2e/login-failure-en.spec.ts` | 로그인 실패 시나리오 7가지 (영문 UI 기준, localStorage locale=en) |

### 테스트 실행 결과물 (`test-results/`)

> **주의**: `test-results/`는 `.gitignore`에 등록되어 있어 GitHub에 올라가지 않습니다.
> 테스트를 직접 실행해야 로컬에 생성됩니다.

| 파일/디렉토리 | 생성 시점 | 설명 |
|---------------|-----------|------|
| `test-results/.last-run.json` | 매 테스트 실행 후 | 마지막 실행 결과 요약 (`status: "passed"`, 실패 목록 등) |
| `test-results/login-failure/` | `login-failure.spec.ts` 실행 시 | 한국어 UI 로그인 실패 결과물 |
| `test-results/login-failure/*.png` | 각 테스트 케이스 실행 시 | `page.screenshot()`으로 직접 저장한 PNG (7장) |
| `test-results/login-failure-en/` | `login-failure-en.spec.ts` 실행 시 | 영문 UI 로그인 실패 결과물 |

#### `test-results/`가 생성되는 원리

**두 가지 경로**로 파일이 만들어집니다.

**① Playwright 자동 생성** — `playwright.config.ts`의 설정에 의해

```ts
use: {
  screenshot: 'only-on-failure',  // 테스트 실패 시 자동으로 스크린샷 저장
}
```

테스트가 실패하면 Playwright가 자동으로 `test-results/{테스트명}/` 경로에 스크린샷을 저장합니다.

**② 스펙 파일에서 직접 저장** — `login-failure.spec.ts`, `login-failure-en.spec.ts`

```ts
// 각 테스트 마지막에 직접 screenshot() 호출
await page.screenshot({ path: 'test-results/login-failure/01-empty-fields.png' })
```

성공/실패 여부와 관계없이 항상 저장됩니다.

#### 생성 명령어

```bash
# 전체 테스트 실행 → test-results/ 전체 생성
npx playwright test

# login-failure 결과물만 생성
npx playwright test e2e/login-failure.spec.ts

# login-failure-en 결과물만 생성
npx playwright test e2e/login-failure-en.spec.ts

# test-results/ 직접 삭제 (재실행 전 초기화할 때)
rm -rf test-results/
```

### Playwright MCP 스크린샷 (`.playwright-mcp/`)

> **주의**: `.playwright-mcp/`는 `.gitignore`에 등록되어 있어 GitHub에 올라가지 않습니다.
> Playwright MCP를 통해 AI가 브라우저를 조작해야 로컬에 생성됩니다.

| 파일 | 설명 |
|------|------|
| `.playwright-mcp/page-{timestamp}.png` | MCP 브라우저 자동화 중 AI가 캡처한 스크린샷 |

---

## 2. `playwright.config.ts` 상세

```ts
export default defineConfig({
  testDir: './e2e',          // 테스트 파일 위치
  timeout: 30_000,           // 테스트 1개당 최대 30초
  retries: 0,                // 실패 시 재시도 없음
  use: {
    baseURL: 'http://localhost:5173',   // 프론트엔드 Vite 개발 서버
    headless: true,                     // 브라우저 UI 없이 실행
    screenshot: 'only-on-failure',      // 실패 시에만 자동 스크린샷
  },
  projects: [
    { name: 'chromium', use: { browserName: 'chromium' } },
  ],
})
```

- **baseURL**: 프론트엔드 Vite 개발 서버 (`localhost:5173`)
- **API**: 백엔드는 `localhost:3000/api` (스펙 파일 내 직접 지정)
- **브라우저**: Chromium 단일 브라우저만 실행

---

## 3. 각 테스트 스펙이 어떻게 사용되었는가

### `INT-01-auth-flow.spec.ts` — 인증 전체 흐름

**목적**: 회원가입부터 로그아웃까지 인증 흐름 전체를 순서대로 검증 (`test.describe.serial` 사용)

| 테스트 | 검증 내용 |
|--------|-----------|
| 1. 회원가입 | API 201 응답, UI 가입 후 자동 로그인 및 홈 리다이렉트 |
| 2. 로그인 | `Set-Cookie`에 `refreshToken` + `HttpOnly` 존재, Access Token은 쿠키에 없음(메모리 저장) |
| 3. 홈 페이지 | 로그인 유지, `/todos` API 200 응답, 검색/필터 UI 요소 노출 |
| 4. 토큰 갱신 | 페이지 새로고침 시 `/auth/refresh` POST 자동 호출 및 200 응답 |
| 5. 로그아웃 | `/auth/logout` 200 응답, `/login` 리다이렉트, 보호 경로 재접근 차단 |
| 6. 중복 이메일 차단 | 동일 이메일 재가입 시 400 에러 및 에러 메시지 노출 |
| 7. 잘못된 자격증명 | API 401 응답, 에러 메시지 body 내용 검증, UI 에러 메시지 노출 |

**특징**:
- `test.describe.serial`: 테스트 간 순서 의존성 보장 (앞 테스트가 만든 계정을 뒤 테스트가 사용)
- `beforeAll`로 테스트 시작 전 계정 사전 생성
- `page.waitForResponse()`로 UI 동작과 API 응답을 동시에 검증

---

### `login-failure.spec.ts` — 로그인 실패 (한국어 UI)

**목적**: 로그인 페이지의 다양한 입력 오류 케이스를 스크린샷으로 시각적으로 기록

| 테스트 | 검증 내용 | API 호출 여부 |
|--------|-----------|---------------|
| 1. 빈 폼 제출 | `/login` URL 유지 | 없음 (프론트 검증) |
| 2. 이메일 형식 오류 | `/login` URL 유지 | 없음 (프론트 검증) |
| 3. 존재하지 않는 이메일 | 401 응답, `/login` 유지 | 있음 |
| 4. 비밀번호 틀림 | 401 응답, `/login` 유지 | 있음 |
| 5. 비밀번호 누락 | `/login` URL 유지 | 없음 (프론트 검증) |
| 6. 이메일 누락 | `/login` URL 유지 | 없음 (프론트 검증) |
| 7. 공백 비밀번호 | `/login` URL 유지 | 있음 |

**특징**:
- 각 테스트마다 `page.screenshot()`으로 `test-results/login-failure/` 에 PNG 저장
- `beforeEach`로 매 테스트 전 로그인 페이지 이동

---

### `login-failure-en.spec.ts` — 로그인 실패 (영문 UI)

**목적**: 영문 locale (`localStorage.setItem('locale', 'en')`) 상태에서 동일한 실패 케이스 검증

- `login-failure.spec.ts`와 동일한 7가지 케이스
- `beforeEach`에서 `localStorage` 조작 후 페이지 reload로 영문 UI 전환
- 스크린샷은 `test-results/login-failure-en/` 에 저장

---

## 4. `test/` 디렉토리란

> **주의**: `test/`는 `.gitignore`에 등록되어 있어 GitHub에 올라가지 않습니다.
> AI가 직접 브라우저를 조작하는 MCP 세션을 실행해야 생성됩니다.

### 생성 주체

`npx playwright test`로 만들어지는 `test-results/`와 달리, `test/`는 **AI가 MCP를 통해 브라우저를 수동 조작하면서 직접 생성**한 디렉토리입니다.

2026-02-12에 Playwright MCP가 실행 불가한 상황에서 **Chrome DevTools MCP**로 대체하여 브라우저를 제어했고, AI가 직접 다음 두 가지를 만들었습니다:

| 파일 | 생성 방법 |
|------|-----------|
| `test/e2e/*.png` | AI가 브라우저 조작 중 `page.screenshot()` 또는 MCP `screenshot` 도구로 직접 캡처 |
| `test/e2e/REPORT.md` | AI가 테스트 결과를 분석한 후 직접 작성한 마크다운 문서 |

### REPORT.md가 만들어지는 방식

`test-results/`의 Playwright 자동 리포트와 달리, `test/e2e/REPORT.md`는 **AI가 수동으로 작성**한 문서입니다.

- AI가 MCP 브라우저로 각 시나리오를 직접 실행
- 실행 결과(PASS/FAIL/PARTIAL)를 판단하고 스크린샷을 저장
- 테스트가 끝난 후 결과를 종합해 REPORT.md를 직접 작성

즉, `코드 자동 실행 → 결과 파일 자동 생성`이 아니라 `AI가 직접 판단 → AI가 직접 문서 작성`입니다.

### 생성 명령어 (AI에게 요청하는 방법)

이 디렉토리는 CLI 명령어로 재현할 수 없습니다. Claude Code에 아래와 같이 요청해야 합니다:

```
docs/3-user-scenario.md 기반으로 E2E 테스트를 수행하고
결과 스크린샷과 REPORT.md를 test/e2e/ 에 저장해줘
```

AI가 MCP 브라우저 도구를 사용해 직접 앱을 조작하고 결과를 기록합니다.

---

## 6. `.playwright-mcp/` 디렉토리란

> **주의**: `.playwright-mcp/`는 `.gitignore`에 등록되어 있어 GitHub에 올라가지 않습니다.
> Playwright MCP를 통해 AI가 브라우저를 조작해야 로컬에 생성됩니다.

**Playwright MCP(Model Context Protocol) 서버**가 생성한 디렉토리이다.

MCP는 Claude Code 같은 AI 도구가 외부 도구와 상호작용하는 표준 프로토콜이다. Playwright MCP를 사용하면 AI가 직접 브라우저를 제어할 수 있다.

- AI가 브라우저를 열고 페이지를 조작할 때마다 스크린샷을 이 디렉토리에 자동 저장
- 파일명 형식: `page-{ISO8601 timestamp}.png`
- 2026-02-12에 이 프로젝트에서 Playwright MCP를 통한 브라우저 자동화 작업이 있었음

**Playwright 테스트(`npx playwright test`)와의 차이점**:

| 구분 | Playwright MCP | Playwright 테스트 |
|------|---------------|------------------|
| 실행 주체 | AI (Claude 등) | 개발자 (`npx playwright test`) |
| 목적 | AI의 브라우저 탐색/조작 | 자동화 E2E 테스트 |
| 스크린샷 위치 | `.playwright-mcp/` | `test-results/` 또는 지정 경로 |
| 설정 파일 | MCP 서버 설정 | `playwright.config.ts` |

---

## 7. 사용 방법

### 5-1. E2E 테스트 실행

테스트 실행 전에 프론트엔드(`:5173`)와 백엔드(`:3000`)가 모두 실행 중이어야 한다.

```bash
# 1. 개발 서버 실행 (별도 터미널)
npm run dev

# 2. 전체 E2E 테스트 실행
npx playwright test

# 3. 특정 스펙만 실행
npx playwright test e2e/INT-01-auth-flow.spec.ts
npx playwright test e2e/login-failure.spec.ts
npx playwright test e2e/login-failure-en.spec.ts

# 4. 브라우저 UI를 보면서 실행 (디버깅용, headless: false)
npx playwright test --headed

# 5. 특정 테스트만 실행 (테스트 이름으로 필터)
npx playwright test --grep "로그인"

# 6. 테스트 결과 HTML 리포트 보기
npx playwright show-report
```

### 5-2. Playwright Inspector (단계별 디버깅)

```bash
# 브라우저를 띄우고 테스트를 한 단계씩 실행
npx playwright test --debug

# 특정 스펙을 디버그 모드로 실행
npx playwright test e2e/INT-01-auth-flow.spec.ts --debug
```

### 5-3. Playwright MCP 사용 (AI 브라우저 자동화)

Claude Code에서 Playwright MCP 서버가 연결되어 있으면 AI가 직접 브라우저를 제어할 수 있다.

**Claude Code MCP 설정 예시** (`~/.claude/settings.json` 또는 `.claude/settings.json`):

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
    }
  }
}
```

**MCP를 통해 AI가 할 수 있는 것**:
- 브라우저를 열고 특정 URL로 이동
- 폼 입력, 버튼 클릭 등 UI 조작
- 페이지 스크린샷 캡처 (자동으로 `.playwright-mcp/`에 저장)
- 페이지 콘텐츠 읽기 및 분석
- 개발 중인 UI 확인 및 피드백

### 5-4. 새 E2E 테스트 추가 방법

```
e2e/
└── 새파일.spec.ts   # 이 위치에 추가하면 자동으로 인식됨
```

기본 구조:

```ts
import { test, expect } from '@playwright/test'

test.describe('테스트 그룹명', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/login')  // baseURL이 자동으로 prepend됨
  })

  test('테스트 이름', async ({ page }) => {
    await page.getByRole('button', { name: '로그인' }).click()
    await expect(page).toHaveURL('/login')
  })
})
```

---

## 8. 디렉토리 구조 요약

```
my-todolist-u/
├── playwright.config.ts          # Playwright 전역 설정
├── e2e/                          # E2E 테스트 스펙
│   ├── INT-01-auth-flow.spec.ts  # 인증 흐름 통합 테스트
│   ├── login-failure.spec.ts     # 로그인 실패 (한국어)
│   └── login-failure-en.spec.ts  # 로그인 실패 (영문)
├── test-results/                 # 테스트 실행 아티팩트
│   ├── .last-run.json            # 마지막 실행 결과 요약
│   ├── login-failure/            # 스크린샷 7장 + REPORT.md
│   └── login-failure-en/        # 영문 UI 결과
└── .playwright-mcp/              # AI MCP 브라우저 자동화 스크린샷 (gitignore)
```
