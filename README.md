# 홈서버 Docker 구성

이 레포지토리는 홈서버 관리를 위한 Docker 서비스 구성을 관리합니다. Traefik을 리버스 프록시로 사용하여 여러 서비스들을 안전하게 관리하고 외부에 노출합니다.

## 포함된 서비스

- Traefik v3.3: 리버스 프록시 및 SSL 인증서 자동화 (Cloudflare DNS 인증 사용)
- ipTIME: 공유기 관리 웹 인터페이스 프록시
- Plex Media Server: 미디어 스트리밍 서버 (하드웨어 가속 지원)
- Deluge: 토렌트 클라이언트 (웹 UI 포함)
- Portainer: Docker 컨테이너 관리 웹 UI (CE 버전)

## 시작하기

1. `.env` 파일 생성 및 설정
   - `.env.example` 파일을 복사하여 `.env` 파일을 생성합니다
   ```bash
   cp .env.example .env
   ```
   - 생성된 `.env` 파일에 실제 값들을 입력합니다:
     
     ### Cloudflare API 설정
     - `CF_API_EMAIL`: Cloudflare 계정 이메일
     - `CF_API_KEY`: Cloudflare Global API Key
     - `CF_API_TOKEN`: Cloudflare API Token

     ### Let's Encrypt 설정
     - `ACME_EMAIL`: SSL 인증서 발급용 이메일 주소

     ### Plex 설정
     - `PLEX_CLAIM`: Plex.tv에서 발급받은 클레임 토큰 (https://www.plex.tv/claim/)
     - `PLEX_ADVERTISE_IP`: Plex 서버 외부 접속 URL (예: https://plex.your-domain.com)

     ### 도메인 설정
     - `TRAEFIK_DASHBOARD_DOMAIN`: Traefik 대시보드 접속 도메인
     - `PLEX_DOMAIN`: Plex 서버 접속 도메인
     - `DELUGE_DOMAIN`: Deluge 웹UI 접속 도메인
     - `PORTAINER_DOMAIN`: Portainer 웹UI 접속 도메인
     - `IPTIME_DOMAIN`: ipTIME 공유기 관리 페이지 접속 도메인

2. Cloudflare 설정
   - Cloudflare 계정에서 필요한 인증 정보를 확인합니다:
     - Global API Key: Cloudflare 대시보드 > Profile > API Tokens
     - API Token: Cloudflare 대시보드 > API Tokens > Create Token
   - 각 서비스의 도메인에 대한 DNS 레코드가 올바르게 설정되어 있는지 확인하세요

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
├── plexmediaserver/    # Plex Media Server 설정 및 라이브러리
├── deluge/            # Deluge 설정 디렉토리
└── portainer/         # Portainer 설정 및 데이터
```

## 포트 설정

- Traefik: 80 (HTTP), 443 (HTTPS)
- ipTIME:
  - 8080: 관리 인터페이스 포트
- Plex Media Server:
  - 32400: 메인 서버 포트
  - 8324, 32469: 추가 서비스 포트
  - 1900, 32410-32414: DLNA 관련 포트
- Deluge:
  - 8112: 웹 UI 포트
  - 6881: 토렌트 포트 (TCP/UDP)
- Portainer:
  - 9000: 웹 UI 포트

## 주의사항

- `.env` 파일에는 민감한 정보가 포함되어 있으므로 절대로 Git에 커밋하지 마세요
- `acme.json` 파일의 권한은 600으로 설정되어야 합니다
- 하드웨어 가속을 위해 Plex 컨테이너에 `/dev/dri` 장치가 마운트되어 있습니다
- 각 서비스의 포트가 다른 프로그램과 충돌하지 않도록 주의하세요
- 정기적으로 Docker 이미지와 컨테이너를 업데이트하여 보안을 유지하세요
- Portainer 초기 설정 시 관리자 계정 생성이 필요합니다
- Portainer의 데이터는 Docker 볼륨에 저장되므로, 백업이 필요한 경우 볼륨을 함께 백업하세요
- iptime 서비스는 공유기의 관리 인터페이스(192.168.0.1:8080)를 안전하게 외부에 노출합니다

## 문제해결

서비스 로그 확인:
```bash
docker compose logs -f [서비스명]
```

모든 서비스 재시작:
```bash
docker compose down && docker compose up -d
```
