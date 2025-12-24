# 프로젝트 구조 (Project Structure)

이 문서는 CTFd 프로젝트의 주요 파일 및 디렉터리 구성을 설명합니다.

## 최상위 디렉터리 (Top-Level Directory)

- **`CTFd/`**: 애플리케이션의 핵심 소스 코드가 포함된 코어 디렉터리입니다.
- **`conf/`**: Nginx, Gunicorn 등의 서버 설정 파일이 위치합니다.
- **`migrations/`**: Alembic 및 Flask-Migrate를 사용한 데이터베이스 마이그레이션 파일들입니다.
- **`scripts/`**: 배포 및 유지보수를 위한 쉘 스크립트 모음입니다.
- **`tests/`**: 프로젝트의 테스트 코드가 포함되어 있습니다.
- **`docker-compose.yml`**: 로컬 개발 및 배포를 위한 Docker 서비스 정의 파일입니다.
- **`Dockerfile`**: CTFd 애플리케이션 이미지를 빌드하기 위한 설정 파일입니다.
- **`manage.py`**: Flask CLI 명령어를 실행하기 위한 엔트리 포인트입니다.
- **`wsgi.py`**: 프로덕션 환경(Gunicorn 등)에서 애플리케이션을 실행하기 위한 WSGI 엔트리 포인트입니다.
- **`requirements.txt`**: Python 의존성 패키지 목록입니다.

## 핵심 애플리케이션 구조 (`CTFd/`)

`CTFd/` 디렉터리는 Flask 애플리케이션의 본체입니다.

- **`__init__.py`**: 애플리케이션 팩토리 패턴(`create_app`)을 구현하고, Flask 앱을 초기화합니다.
- **`models/`**: SQLAlchemy를 사용한 데이터베이스 스키마 모델이 정의되어 있습니다. (`Users`, `Teams`, `Challenges` 등)
- **`api/`**: REST API 엔드포인트가 정의된 블루프린트입니다. (`v1` 하위 디렉터리에 버전별 구현 존재)
- **`admin/`**: 관리자 페이지와 관련된 뷰 및 로직입니다.
- **`auth/`**: 로그인, 회원가입 등 인증 관련 기능입니다.
- **`challenges/`**: 문제(Challenge) 목록 및 상세 보기를 처리하는 로직입니다.
- **`forms/`**: WTForms를 사용한 입력 폼 정의입니다. (로그인, 회원가입, 문제 생성 등)
- **`schemas/`**: Marshmallow를 사용한 데이터 직렬화/역직렬화 스키마입니다. API 응답 및 요청 검증에 사용됩니다.
- **`plugins/`**: CTFd의 기능을 확장하기 위한 플러그인 시스템의 기반 및 기본 플러그인이 위치합니다.
- **`themes/`**: 사용자에게 보여지는 UI 테마(HTML 템플릿, 정적 파일) 디렉터리입니다. (`core`, `admin` 테마 등)
- **`utils/`**: 헬퍼 함수, 데코레이터, 설정 관리 등 유틸리티 모듈입니다.
- **`constants/`**: 프로젝트 전반에서 사용되는 상수 값들이 정의되어 있습니다.

## 주요 설정 파일

- **`config.py`**: Flask 애플리케이션의 기본 설정(시크릿 키, 캐시 타입 등)을 정의합니다.
- **`docker-compose.yml`**: `ctfd`, `db` (MariaDB), `cache` (Redis), `nginx` 4개의 서비스를 정의하여 전체 스택을 실행합니다.
