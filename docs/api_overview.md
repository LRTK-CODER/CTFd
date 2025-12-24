# API 개요 (API Overview)

CTFd는 RESTful JSON API를 제공하여 프론트엔드와 통신하고 자동화를 지원합니다.

## 기본 정보

- **Base URL**: `/api/v1`
- **인증**:
  - **세션 인증**: 브라우저 기반 접근 시 세션 쿠키를 사용합니다. `CSRF-Token` 헤더가 필요합니다.
  - **토큰 인증**: API 토큰을 발급받아 `Authorization: Token <token>` 헤더를 사용할 수 있습니다.
- **Content-Type**: 요청 및 응답 본문은 `application/json` 형식을 사용합니다.

## 주요 리소스 (Key Resources)

API 엔드포인트는 `CTFd/api/v1` 디렉터리에 정의되어 있습니다.

### Users & Teams
- `GET /api/v1/users`: 사용자 목록 조회.
- `GET /api/v1/users/{user_id}`: 특정 사용자 상세 정보.
- `POST /api/v1/users`: (Admin) 사용자 생성.
- `GET /api/v1/teams`: 팀 목록 조회.

### Challenges
- `GET /api/v1/challenges`: 문제 목록 조회.
- `GET /api/v1/challenges/{challenge_id}`: 문제 상세 정보.
- `POST /api/v1/challenges/attempt`: 정답 플래그 제출 및 검증.
- `GET /api/v1/challenges/{challenge_id}/solves`: 해당 문제를 푼 사용자 목록.

### Scoreboard
- `GET /api/v1/scoreboard`: 현재 스코어보드 데이터 조회.

### Information
- `GET /api/v1/configs`: 공개된 시스템 설정 값 조회.

### Utility
- `POST /api/v1/files`: 파일 업로드 (Admin).

## 스키마 (Schemas)

응답 데이터의 구조는 `CTFd/schemas/` 또는 `CTFd/api/v1/schemas/`에 정의된 Marshmallow 스키마를 따릅니다. 이는 일관된 응답 포맷을 보장합니다.

> **참고**: API의 상세 명세(Swagger/OpenAPI)는 기본적으로 제공되지 않지만, 코드의 `api` 블루프린트와 관련 스키마를 참조하여 파악할 수 있습니다.
