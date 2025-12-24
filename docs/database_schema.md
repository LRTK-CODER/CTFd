# 데이터베이스 스키마 (Database Schema)

CTFd는 관계형 데이터베이스(MariaDB/MySQL)를 사용하며, SQLAlchemy ORM을 통해 모델을 정의합니다. 주요 모델은 `CTFd/models/__init__.py`에 정의되어 있습니다.

## 핵심 모델 (Core Models)

### `Users` (`users` 테이블)
- **설명**: 시스템의 사용자 계정입니다.
- **주요 필드**: `name`, `email`, `password`, `type` (user/admin), `team_id`, `score`.
- **관계**: `Teams`에 소속될 수 있으며, `Solves`, `Fails`, `Awards`와 1:N 관계를 가집니다.

### `Teams` (`teams` 테이블)
- **설명**: 사용자들의 그룹인 팀입니다 (팀 모드 활성화 시 사용).
- **주요 필드**: `name`, `email`, `password` (입장 코드), `captain_id`.
- **관계**: 여러 `Users`를 멤버로 가집니다.

### `Challenges` (`challenges` 테이블)
- **설명**: 사용자가 풀어야 할 CTF 문제입니다.
- **주요 필드**:
  - `name`: 문제 이름.
  - `description`: 문제 설명 (마크다운).
  - `value`: 문제의 점수.
  - `category`: 문제 카테고리 (Web, Pwnable 등).
  - `type`: 문제 유형 (`standard`, `dynamic` 등). 플러그인으로 확장 가능합니다.
  - `state`: 문제 공개 상태 (`visible`, `hidden`).

### `Flags` (`flags` 테이블)
- **설명**: 문제의 정답(해답)입니다.
- **주요 필드**: `content` (플래그 문자열), `type` (static, regex), `challenge_id`.

## 게임 로직 모델 (Game Logic Models)

### `Solves` (`solves` 테이블)
- **설명**: 사용자가 문제를 성공적으로(정답 플래그 제출) 푼 기록입니다.
- **주요 필드**: `user_id`, `team_id`, `challenge_id`, `date`.
- **제약**: (user_id/team_id, challenge_id) 쌍은 유니크해야 합니다 (중복 풀이 방지).

### `Fails` (`fails` 테이블)
- **설명**: 사용자가 오답을 제출한 기록입니다.
- **주요 필드**: `user_id`, `team_id`, `challenge_id`, `date`.

### `Awards` (`awards` 테이블)
- **설명**: 사용자나 팀에게 부여되는 보너스 점수 또는 힌트 차감 등입니다.
- **주요 필드**: `value` (점수, 음수 가능), `name`, `user_id`, `team_id`.

### `Hints` (`hints` 테이블)
- **설명**: 문제에 대한 힌트입니다.
- **주요 필드**: `content`, `cost` (열람 시 차감되는 점수), `challenge_id`.

## CMS 및 기타 모델 (CMS & Others)

### `Pages` (`pages` 테이블)
- **설명**: 관리자가 생성한 정적 페이지입니다.
- **주요 필드**: `route` (접근 경로), `content` (HTML/Markdown).

### `Files` (`files` 테이블)
- **설명**: 문제 첨부 파일이나 페이지 관련 파일들의 메타데이터입니다. 실제 파일은 파일 시스템이나 S3에 저장됩니다.
- **주요 필드**: `location` (파일 경로), `challenge_id`, `page_id`.

### `Notifications` (`notifications` 테이블)
- **설명**: 사용자에게 보내는 알림 메시지입니다.

### `Tracking` (`tracking` 테이블)
- **설명**: 사용자 활동 로깅(IP 주소 등)을 위한 모델입니다.
