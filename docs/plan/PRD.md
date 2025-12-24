# CTFd 차세대 커스텀 개발 PRD (Product Requirements Document)

## 1. 개요 (Overview)

본 문서는 기존 CTFd 플랫폼을 현대적인 아키텍처와 기능으로 업그레이드하기 위한 커스텀 개발 요구사항을 정의합니다. 주요 목표는 **시스템 분리(Decoupling)**, **현대적 UI/UX**, **보안성 높은 문제 환경**, **AI 기반 학습 보조**, 그리고 **관리 효율성 증대**입니다.

## 2. 주요 기능 요구사항 (Key Features)

> 우선순위에 따라 작성되었습니다.

### 2.1. 프론트엔드/백엔드 분리 (Decoupled Architecture)
**목표**: 모놀리식 구조를 탈피하여 확장성과 배포 유연성을 확보합니다.

- **AS-IS**: Flask가 Jinja2 템플릿을 사용하여 HTML을 서버 사이드 렌더링(SSR). 정적 파일과 API가 혼재됨.
- **TO-BE**:
  - **Backend (API Server)**: Flask는 REST API (JSON) 만을 제공하는 Headless 서버로 전환.
    - 기존 `v1` API를 보강하여 화면 렌더링에 필요한 모든 데이터를 API로 제공.
    - Session 기반 인증과 더불어 JWT 기반 인증 도입 고려 (Stateless).
  - **Frontend (Client)**: React 기반의 SPA (Single Page Application).
    - Nginx 혹은 별도의 Node.js 서버를 통해 정적 파일 서빙.
  - **Infra**: 백엔드 컨테이너(API)와 프론트엔드 컨테이너(Static/Node)를 분리하여 독립적으로 스케일링 가능하게 구성 (K8s 친화적).

### 2.2. UI/UX 전면 개편 (Modern UI/UX)
**목표**: 사용자 경험을 극대화하는 반응형 및 인터랙티브 디자인 도입.

- **Stack**: React, Vite, TailwindCSS, Framer Motion (애니메이션).
- **상세 요건**:
  - **대시보드**: 실시간 점수 변동 그래프 및 활동 로그 시각화.
  - **문제 뷰**: 터미널 스타일의 웹 CLI 통합, 다크 모드/라이트 모드 기본 지원.
  - **반응형**: 모바일 및 태블릿 완벽 지원.
  - **퍼포먼스**: 페이지 로딩 속도 최적화 (Code Splitting).

### 2.3. 문제별 독립 컨테이너 환경 (Isolated Challenge Environments)
**목표**: 사용자 요청 시 즉시 생성되는 안전한 1인 1컨테이너 환경 제공.

- **구현 방식**:
  - **On-Demand**: 사용자가 "문제 시작" 버튼을 누르면 동적으로 컨테이너 생성.
  - **수명주기 관리**: 제한 시간(예: 1시간) 후 자동 삭제, 연장 기능 제공.
- **보안 아키텍처 (Critical)**:
  - **Docker API 보호**: CTFd 백엔드가 직접 Docker Socket에 접근하지 않도록 **Middleware(Orchestrator)** 도입.
    - *CTFd Backend -> Orchestrator API -> Docker/LXD Daemon*
    - Orchestrator는 허용된 이미지와 리소스 제한(CPU, RAM, Network) 정책만 수행하도록 제어.
  - **네트워크 격리**: 문제 컨테이너는 인터넷 접속을 차단(Outbound Block)하고, 사용자에게는 특정 포트만 포워딩.

### 2.4. LLM 기반 힌트 자동화 (AI-Powered Hints)
**목표**: 고정된 힌트가 아닌, 사용자의 풀이 단계에 맞춘 맞춤형 가이드 제공.

- **워크플로우**:
  1. **문제 등록**: 출제자가 문제와 함께 "풀이 보고서(Write-up)"를 업로드 (Markdown/PDF).
  2. **지식 베이스 구축**: 풀이 보고서를 파싱하여 Vector DB (Chroma/Pinecone) 혹은 로컬 임베딩 저장.
  3. **사용자 질문**: 사용자가 "힌트 주세요" 요청 혹은 질문 입력.
  4. **추론 (RAG)**: LLM이 사용자의 현재 상태(푼 내역 등)와 풀이 보고서를 참조하여 정답을 직접 노출하지 않고 다음 단계로 유도하는 힌트 생성.
- **제약 사항**: 프롬프트 엔지니어링을 통해 "정답(Flag)을 절대 직접 말하지 말 것" 설정 필수.

### 2.5. 관리자 기능 확대 (Extended Administration)
**목표**: 대규모 대회 운영 및 정보 공유를 위한 CMS 기능 강화.

- **RBAC (Role-Based Access Control)**:
  - 기존 `Admin`, `User` 외에 `Manager`(문제 관리만 가능), `Reviewer`(풀이 검토만 가능) 등 커스텀 그룹/권한 생성 기능.
- **CMS 기능**:
  - **블로그/공지사항**: 마크다운 에디터를 내장한 게시판 기능.
  - **자료실**: 대회 룰북, 바이너리 툴 등을 공유할 수 있는 파일 자료실.
  - **권한 제어**: 특정 그룹에게만 게시물 노출 설정.

## 3. 기술 스택 제안 (Tech Stack Proposal)

| 구분 | 기술 스택 | 비고 |
|:---:|:---:|:---|
| **Frontend** | React, TypeScript, TailwindCSS, TanStack Query | 모던 웹 표준 준수 |
| **Backend** | Flask (Python), Celery, Redis | 기존 코드베이스 활용 및 비동기 작업 처리 |
| **Database** | MariaDB | 기존 데이터 마이그레이션 용이 |
| **AI/LLM** | OpenAI API 또는 Local LLM (Llama 3), LangChain | 자연어 처리 및 RAG 구현 |
| **Infra** | Docker Compose (Dev), Kubernetes (Prod) | 마이크로서비스 아키텍처 지향 |
| **Security** | Traefik (Proxy), Falco (Container Security) | 보안 및 모니터링 |

## 4. 로드맵 (Phased Roadmap)

### Phase 1: 아키텍처 분리 및 기본 UI 구축
- Flask 템플릿 렌더링 제거 및 REST API 화.
- React 프로젝트 초기화 및 기본 인증(로그인/가입) 구현.
- 기본 대시보드 및 스코어보드 이식.

### Phase 2: 인프라 고도화 및 컨테이너 오케스트레이션
- 문제 컨테이너 오케스트레이터 미들웨어 개발.
- Docker API 보안 래퍼 구현.
- 프론트엔드에서 컨테이너 생성/접속 UI 연동.

### Phase 3: AI 기능 및 심화 기능
- 풀이 보고서 업로드 및 벡터 DB 연동.
- LLM 챗봇 인터페이스 개발.
- 관리자 CMS 및 권한 관리 시스템 구현.
