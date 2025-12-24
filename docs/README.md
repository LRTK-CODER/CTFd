# CTFd 문서 (CTFd Documentation)

이 문서는 CTFd 프로젝트의 코드베이스, 인프라, 아키텍처 및 API에 대한 분석 내용을 담고 있습니다. 프로젝트를 커스터마이징하거나 기능을 확장하고자 하는 개발자를 위해 작성되었습니다.

## 목차 (Table of Contents)

1. [프로젝트 구조 (Project Structure)](project_structure.md)
   - 파일 및 디렉터리 구조에 대한 상세 설명.
   
2. [인프라스트럭처 (Infrastructure)](infrastructure.md)
   - Docker Compose를 이용한 배포 환경 및 설정.

3. [애플리케이션 아키텍처 (Application Architecture)](application_architecture.md)
   - Flask 프레임워크 기반의 설계, 플러그인 시스템, 테마 등.
   
4. [데이터베이스 스키마 (Database Schema)](database_schema.md)
   - 사용자, 문제, 풀이 등 주요 데이터 모델 설명.
   
5. [API 개요 (API Overview)](api_overview.md)
   - REST API v1의 주요 엔드포인트 및 사용법.

## 시작하기

로컬 개발 환경을 설정하려면 `infrastructure.md`를 참고하여 Docker 컨테이너를 실행하십시오. 코드 수정을 시작하기 전에 `project_structure.md`를 읽어 파일 위치를 파악하는 것을 권장합니다.
