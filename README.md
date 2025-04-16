# 홈서버 Docker 구성

이 레포지토리는 홈서버 관리를 위한 Docker 서비스 구성을 관리합니다. Traefik을 리버스 프록시로 사용하여 여러 서비스들을 안전하게 관리하고 외부에 노출합니다.

## 포함된 서비스

- Traefik: 리버스 프록시 및 SSL 인증서 자동화

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
└── traefik/
    ├── acme/           # Let's Encrypt 인증서 저장소
    └── config/         # Traefik 설정 파일들
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
