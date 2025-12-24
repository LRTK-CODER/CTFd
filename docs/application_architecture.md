# 애플리케이션 아키텍처 (Application Architecture)

CTFd는 확장성과 모듈화에 중점을 둔 Flask 기반 웹 애플리케이션입니다.

## 핵심 기술 스택 (Core Stack)

- **Framework**: [Flask](https://flask.palletsprojects.com/) - 웹 애플리케이션 프레임워크.
- **Database ORM**: [SQLAlchemy](https://www.sqlalchemy.org/) - 데이터베이스 모델링 및 쿼리.
- **Serialization**: [Marshmallow](https://marshmallow.readthedocs.io/) - 복잡한 데이터 타입의 직렬화/역직렬화 (API 응답 등에 사용).
- **Frontend**: Jinja2 템플릿 엔진, Bootstrap (기본 테마), Alpine.js (일부 동적 기능).

## 애플리케이션 팩토리 (Application Factory)

`CTFd/__init__.py`의 `create_app` 함수는 애플리케이션 팩토리 패턴을 구현합니다.
1. Flask 인스턴스 생성 (`CTFdFlask`).
2. 설정을 로드 (`config.py`).
3. 데이터베이스 (`db`), 마이그레이션 도구, 캐시 등을 초기화.
4. 블루프린트(Blueprint) 등록.
5. 플러그인 로드 (`init_plugins`).

## 블루프린트 (Blueprints)

기능별로 분리된 모듈(Blueprint)로 구성되어 있습니다:

- **Views (`views.py`)**: 정적 페이지, 사용자 프로필 등 일반적인 웹 페이지 라우트.
- **Auth (`auth/`)**: 로그인, 회원가입, 자격 증명 확인.
- **Admin (`admin/`)**: `/admin` 경로 하위의 관리자 전용 기능.
- **API (`api/`)**: `/api/v1` 경로 하위의 RESTful API.
- **Challenges (`challenges/`)**: 문제 목록 및 제출 처리.

## 플러그인 시스템 (Plugin System)

CTFd는 강력한 플러그인 시스템을 제공하여 코어 코드를 수정하지 않고도 기능을 확장할 수 있습니다.
- **위치**: `CTFd/plugins/` 디렉터리에 위치.
- **로딩**: `load(app)` 함수를 통해 앱 실행 시 동적으로 로드됨.
- **기능**:
  - 새로운 라우트 추가.
  - 기존 템플릿 오버라이드.
  - 스크립트 및 스타일시트 주입 (`register_plugin_script` 등).
  - 데이터베이스 모델 확장.

## 테마 시스템 (Theme System)

사용자 인터페이스는 테마를 통해 완전히 커스터마이징 가능합니다.
- **위치**: `CTFd/themes/`
- **구조**: 각 테마는 `templates/` (HTML)와 `static/` (CSS/JS/Images) 디렉터리를 가집니다.
- **설정**: 관리자 페이지에서 활성 테마를 변경할 수 있습니다.
- **Jinja2 로더**: `ThemeLoader`를 통해 활성화된 테마의 템플릿을 우선적으로 로드합니다.
