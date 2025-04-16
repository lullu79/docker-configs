# 홈서버 Docker 구성

이 레포지토리는 홈서버 관리를 위한 Docker 서비스 구성을 관리합니다. Traefik을 리버스 프록시로 사용하여 여러 서비스들을 안전하게 관리하고 외부에 노출합니다.

## 포함된 서비스

- Traefik: 리버스 프록시 및 SSL 인증서 자동화
- Plex Media Server: 미디어 스트리밍 서버

## 시작하기

1. `.env` 파일 생성
   - `.env.example` 파일을 복사하여 `.env` 파일을 생성합니다
   ```bash
   cp .env.example .env
   ```
   - 생성된 `.env` 파일에 실제 값들을 입력합니다
   - 도메인, API 키, 비밀번호 등 필요한 설정을 모두 입력해야 합니다

2. Cloudflare 설정
   - Cloudflare 계정의 이메일과 Global API Key가 필요합니다
   - Global API Key는 Cloudflare 대시보드 > Profile > API Tokens에서 확인할 수 있습니다
   - DNS 레코드가 올바르게 설정되어 있는지 확인하세요

3. 네트워크 생성
   ```bash
   docker network create traefik-proxy
   ```

4. 서비스 시작
   ```bash
   docker compose up -d
   ```

## 디렉토리 구조

```
.
├── docker-compose.yml    # 메인 Docker Compose 설정
├── .env                 # 환경 변수 (gitignore에 포함)
├── .env.example         # 환경 변수 예제
├── traefik/
│   ├── acme/           # Let's Encrypt 인증서 저장소
│   └── config/         # Traefik 설정 파일들
└── plexmediaserver/    # Plex Media Server 설정 및 라이브러리
    └── Library/        # Plex 라이브러리 및 설정 파일들
```

## 주의사항

- `.env` 파일에는 민감한 정보가 포함되어 있으므로 절대로 Git에 커밋하지 마세요
- 실제 설정 시에는 `.env.example`을 참고하여 `.env` 파일을 생성하세요
- 각 서비스의 포트가 다른 프로그램과 충돌하지 않도록 주의하세요
- 정기적으로 Docker 이미지와 컨테이너를 업데이트하여 보안을 유지하세요

## 문제해결

서비스 로그 확인:
```bash
docker compose logs -f [서비스명]
```

모든 서비스 재시작:
```bash
docker compose down && docker compose up -d
```

# Docker Configs

## 설정 방법

1. `.env.example` 파일을 `.env`로 복사합니다:
   ```bash
   cp .env.example .env
   ```

2. `.env` 파일을 열어 실제 값으로 환경 변수를 설정합니다:
   - `ACME_EMAIL`: Let's Encrypt 인증서 발급용 이메일
   - `CF_API_EMAIL`: Cloudflare 계정 이메일
   - `CF_API_KEY`: Cloudflare API 키
   - `TRAEFIK_DASHBOARD_DOMAIN`: Traefik 대시보드 접속 도메인

3. Docker 컨테이너를 시작합니다:
   ```bash
   docker-compose up -d
   ```

## 보안 주의사항

- `.env` 파일은 민감한 정보를 포함하고 있으므로 절대로 깃허브에 커밋하지 마세요.
- `acme.json` 파일의 권한은 600으로 설정되어야 합니다.
