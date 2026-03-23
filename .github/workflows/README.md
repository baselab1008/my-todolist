# GitHub Actions Workflows

`main` 브랜치에 푸시될 때 변경된 경로에 따라 자동으로 Vercel 프로덕션 배포를 트리거합니다.

## 워크플로우 목록

| 파일 | 이름 | 트리거 경로 |
|------|------|------------|
| `backend-prod.yaml` | Backend Production | `backend/**` |
| `frontend-prod.yaml` | Frontend Production | `frontend/**` |

## 동작 방식

- `backend/**` 변경 시 → 백엔드 Vercel 프로젝트에 프로덕션 배포
- `frontend/**` 변경 시 → 프론트엔드 Vercel 프로젝트에 프로덕션 배포

## 필요한 GitHub Secrets

GitHub 저장소의 **Settings > Secrets and variables > Actions** 에 아래 값을 등록해야 합니다.

| Secret 이름 | 설명 |
|-------------|------|
| `VERCEL_TOKEN` | Vercel 계정 액세스 토큰 |
| `VERCEL_ORG_ID` | Vercel 조직(팀) ID |
| `VERCEL_PROJECT_ID_BACKEND` | 백엔드 Vercel 프로젝트 ID |
| `VERCEL_PROJECT_ID_FRONTEND` | 프론트엔드 Vercel 프로젝트 ID |
