# 인프라스트럭처 (Infrastructure)

CTFd는 Docker Compose를 사용하여 관리하기 쉬운 컨테이너 기반 환경을 제공합니다. 이 문서는 `docker-compose.yml` 파일에 정의된 인프라 구성을 설명합니다.

## 서비스 구성 (Services)

CTFd는 4개의 주요 서비스로 구성됩니다.

### 1. `ctfd` (Web Application)
- **이미지**: 현재 디렉터리의 `Dockerfile`을 빌드하여 생성 (`build: .`).
- **포트**: 호스트의 8000번 포트를 컨테이너의 8000번 포트로 매핑.
- **역할**: 핵심 Flask 웹 애플리케이션을 실행.
- **볼륨**:
  - `.data/CTFd/logs` -> `/var/log/CTFd`: 로그 파일 저장.
  - `.data/CTFd/uploads` -> `/var/uploads`: 사용자가 업로드한 파일(문제 파일 등) 저장.
  - `.` -> `/opt/CTFd` (Read-Only): 소스 코드를 마운트하여 개발 시 코드 변경 사항 반영 (프로덕션 설정이나 빌드 방식에 따라 다를 수 있음).
- **의존성**: `db` 서비스가 시작된 후 실행.

### 2. `db` (Database)
- **이미지**: `mariadb:10.11`
- **역할**: 영구 데이터 저장소 (사용자, 문제, 풀이 기록 등).
- **환경 변수**:
  - `MARIADB_ROOT_PASSWORD`, `MARIADB_USER`, `MARIADB_PASSWORD`, `MARIADB_DATABASE` 등이 `ctfd` 값으로 기본 설정됨.
- **볼륨**:
  - `.data/mysql` -> `/var/lib/mysql`: 데이터베이스 파일 영구 저장.

### 3. `cache` (Redis)
- **이미지**: `redis:4`
- **역할**: 서버 측 세션 저장, 데이터 캐싱, 백그라운드 작업 큐 등을 처리.
- **볼륨**:
  - `.data/redis` -> `/data`: Redis 데이터 영구 저장.

### 4. `nginx` (Reverse Proxy)
- **이미지**: `nginx:stable`
- **역할**: `ctfd` 서비스 앞단에서 웹 요청을 받아 처리하는 리버스 프록시.
- **포트**: 호스트의 80번 포트를 컨테이너의 80번 포트로 매핑.
- **설정**: `./conf/nginx/http.conf` 파일이 `/etc/nginx/nginx.conf`로 마운트됨.

## 네트워크 (Networks)

- **`default`**: 외부와 통신하거나 포트 포워딩을 위해 사용되는 기본 네트워크.
- **`internal`**: 내부 서비스(`ctfd`, `db`, `cache`) 간의 통신을 위한 격리된 네트워크. `internal: true`로 설정되어 외부 접근이 제한됨.

## 환경 변수 (Environment Variables)

`ctfd` 서비스는 다음과 같은 주요 환경 변수를 사용합니다:

- `UPLOAD_FOLDER`: 업로드 파일 저장 경로 (`/var/uploads`).
- `DATABASE_URL`: 데이터베이스 연결 문자열 (`mysql+pymysql://...`).
- `REDIS_URL`: Redis 연결 문자열 (`redis://cache:6379`).
- `WORKERS`: Gunicorn 워커 프로세스 수.
- `REVERSE_PROXY`: 리버스 프록시 뒤에서 실행 중임을 알리는 플래그.
