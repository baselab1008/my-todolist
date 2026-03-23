# Ubuntu 설치 가이드 (Windows)

Windows에서 WSL을 통해 Ubuntu를 설치하는 방법입니다.
Ubuntu 설치 후 **[setup-ubuntu.md](./setup-ubuntu.md)** 를 따라 진행하세요.

---

## 1. WSL 설치

PowerShell을 **관리자 권한**으로 실행 후:

```powershell
wsl --install
```

---

## 2. 시스템 아키텍처 확인

**설정 > 시스템 > 정보 > 시스템 종류** 확인:

| 표시 내용 | 아키텍처 |
|---|---|
| 64비트 운영 체제, x64 기반 프로세서 | AMD64 (Intel/AMD CPU) |
| 64비트 운영 체제, ARM 기반 프로세서 | ARM64 (스냅드래곤 등) |

---

## 3. Docker Desktop 설치

- [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/) 에서 다운로드
- 설치 옵션:
  - **`Use WSL 2 instead of Hyper-V`**: 반드시 체크
  - **`Allow Windows containers...`**: 체크하지 않음

---

## 4. Ubuntu 설치

```powershell
wsl --install -d Ubuntu-24.04
# 계정: sogang / 비밀번호: root
```

> Ubuntu 22.04 이하는 패키지 충돌이 발생할 수 있으므로 반드시 24.04 이상을 설치하세요.

> 이후 설정은 **[setup-ubuntu.md](./setup-ubuntu.md)** 를 따라 진행하세요.
