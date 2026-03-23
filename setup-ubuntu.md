# 개발 환경 설정 가이드 (Ubuntu)

> Windows 환경이라면 WSL Ubuntu 터미널에서 실행하세요.
> Windows 초기 설정은 **[setup-windows.md](./setup-windows.md)** 를 참고하세요.

---

## 1. PostgreSQL 설치

```bash
sudo apt update
sudo apt install -y postgresql postgresql-contrib
sudo service postgresql start
sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'post';"
```

---

## 2. 프로젝트 클론

```bash
cd ~
git clone https://github.com/baselab1008/my-todolist
cd my-todolist
```

---

## 3. 셋업 스크립트 실행 (Node.js 설치 + DB 초기화 + 의존성 설치 + 환경변수 생성 자동화)

아래 명령어 하나로 Node.js 설치, DB 초기화, npm 의존성 설치, `.env` 파일 생성까지 모두 자동으로 처리됩니다.

```bash
chmod +x tododb-setup.sh
./tododb-setup.sh
```

### 설치 확인

```bash
node -v          # v20.x.x 이상
npm -v           # 10.x.x 이상
psql --version   # psql 17.x 이상
```

---

## 4. 애플리케이션 실행

```bash
npm run dev
```

| 항목 | 주소 |
|---|---|
| 프론트엔드 | http://localhost:5173 |
| 백엔드 API | http://localhost:3000 |
| API 문서 (Swagger) | http://localhost:3000/api-docs |

**테스트 계정:**
- `alice@example.com` / `Password1!`
- `bob@example.com` / `Password1!`

---

## 5. 배포 환경변수 설정

프로덕션 배포 전 프론트엔드 환경변수 파일을 직접 생성해야 합니다.

```bash
cp frontend/.env.example frontend/.env.production
```

이후 `frontend/.env.production` 파일을 열어 아래 내용을 실제 값으로 채웁니다:

| 변수명 | 설명 | 예시 |
|--------|------|------|
| `VITE_API_BASE_URL` | 배포된 백엔드 API의 URL | `https://your-backend.vercel.app/api` |

> `.env.production`은 보안상 `.gitignore`에 포함되어 있으므로 GitHub에 올라가지 않습니다.

---

## 트러블슈팅

### 외부에서 접속 시 ERR_CONNECTION_TIMED_OUT

```bash
# 방화벽 상태 확인
sudo ufw status

# 포트 허용
sudo ufw allow 5173
sudo ufw allow 3000

# 방화벽 활성화 (비활성 상태인 경우)
sudo ufw enable
```

> AWS / GCP / Azure 등 클라우드 서버라면 콘솔에서 **인바운드 보안 그룹 규칙**에 5173, 3000 포트를 추가해야 합니다.

---

## 참고. Swagger Mock 서버 설정 및 사용법

백엔드 구현 전에 프론트엔드를 개발하거나, API 명세(요청/응답 형식)를 미리 확인하기 위한 목서버입니다.
실제 DB나 비즈니스 로직은 없으며, `swagger.json` 명세 기반으로 가짜 응답을 자동 생성합니다.

### 의존성 설치 (최초 1회)

```bash
cd mockup
npm install
cd ..
```

### 실행 (프로젝트 루트에서)

```bash
npm run dev:mock
```

| 서비스 | 주소 |
|---|---|
| 목서버 API | http://localhost:4010/api |
| Swagger UI | http://localhost:4010/docs |
| 프론트엔드 | http://localhost:5173 |

### Swagger UI에서 API 테스트하는 법

1. http://localhost:4010/docs 접속
2. 테스트할 API 엔드포인트 클릭
3. **Try it out** 버튼 클릭
4. 파라미터 입력 후 **Execute** 클릭
5. `swagger.json` 명세 기반으로 자동 생성된 가짜 응답 확인
