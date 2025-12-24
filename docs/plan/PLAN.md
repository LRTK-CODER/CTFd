# Implementation Plan: CTFd 차세대 커스텀 개발 - Phase 1

**Status**: 🔄 In Progress
**Started**: 2025-12-24
**Last Updated**: 2025-12-24
**Estimated Completion**: 2025-01-31

---

**⚠️ CRITICAL INSTRUCTIONS**: After completing each phase:
1. ✅ Check off completed task checkboxes
2. 🧪 Run all quality gate validation commands
3. ⚠️ Verify ALL quality gate items pass
4. 📅 Update "Last Updated" date above
5. 📝 Document learnings in Notes section
6. ➡️ Only then proceed to next phase

⛔ **DO NOT skip quality gates or proceed with failing checks**

---

## 📋 Overview

### Feature Description
CTFd 플랫폼을 현대적인 아키텍처로 전환하기 위한 첫 번째 단계입니다. 프론트엔드와 백엔드를 물리적으로 분리(Decoupling)하고, React 기반의 새로운 UI를 구축합니다. Flask 서버는 API 전용 서버로 전환됩니다.

### Success Criteria
- [ ] Flask 서버가 화면 렌더링(Server-Side Rendering)을 수행하지 않고 JSON API만 반환한다.
- [ ] React 기반의 프론트엔드 애플리케이션이 빌드되어 Nginx 등을 통해 서빙된다.
- [ ] 프론트엔드에서 백엔드 API를 호출하여 로그인 및 기본 대시보드가 정상 작동한다.
- [ ] Docker Compose 환경에서 프론트엔드(Client)와 백엔드(API) 서비스가 분리되어 실행된다.

### User Impact
- **개발자**: 프론트엔드/백엔드 독립적 개발 및 배포 가능.
- **사용자**: 페이지 로딩 속도 향상(SPA) 및 현대적인 UI 경험.

---

## 🏗️ Architecture Decisions

| Decision | Rationale | Trade-offs |
|----------|-----------|------------|
| **SPA (Single Page App)** | 반응형 사용자 경험 및 리치 클라이언트 기능 구현 | SEO 최적화 복잡성 증가 (CTF 플랫폼 특성상 덜 중요) |
| **Flask API Only** | 기존 비즈니스 로직 유지하며 아키텍처만 변경 | RESTful API 설계 및 문서화 작업 추가 필요 |
| **React + Vite** | 빠른 개발 속도와 최신 프론트엔드 생태계 활용 | 팀의 React 학습 곡선 (기존 Jinja2 대비) |

---

## 📦 Dependencies

### Required Before Starting
- [ ] `docs/api_overview.md` (기존 API 문서)
- [ ] Node.js v18+ 환경

### External Dependencies
- Frontend: `React`, `Vite`, `TailwindCSS`, `Axios`
- Backend: `Flask-CORS` (API 통신 허용)

---

## 🧪 Test Strategy

### Testing Approach
**TDD Principle**: API 엔드포인트 테스트와 프론트엔드 컴포넌트 테스트를 우선 작성합니다.

### Test Pyramid for This Feature
| Test Type | Coverage Target | Purpose |
|-----------|-----------------|---------|
| **Unit Tests (BE)** | ≥80% | Flask API 응답 포맷 및 상태 코드 검증 |
| **Unit Tests (FE)** | ≥70% | React 컴포넌트 렌더링 및 상태 관리 검증 |
| **E2E Tests** | Critical Paths | 로그인 -> 대시보드 진입 흐름 검증 |

### Test File Organization
```
CTFd/
├── tests/              # Backend Tests
│   └── api/            # API Route Tests (New)
frontend/
└── src/
    └── __tests__/      # Frontend Tests
```

---

## 🚀 Implementation Phases

### Phase 1: Complete Backend Transformation (API-First)
**Goal**: Flask 애플리케이션을 완전한 API 전용 서버(Headless)로 전환합니다. UI 렌더링 로직을 제거하고, 프론트엔드에서 필요한 모든 데이터를 REST API로 제공할 수 있도록 만듭니다.
**Estimated Time**: 40 hours
**Status**: ⏳ Pending

#### Tasks

**🔴 RED: Write Failing Tests First**
- [ ] **Test 1.1**: 메인 페이지 및 주요 기능 HTML 렌더링 제거 확인
  - File(s): `CTFd/tests/api/test_routes.py`
  - Expected: HTML 응답이 아닌 JSON 에러 또는 데이터 반환 기대.
  - Details: API화 되지 않은 기존 View 함수들에 대한 테스트.

- [ ] **Test 1.2**: 인증(Authentication) API 검증
  - File(s): `CTFd/tests/api/test_auth.py`
  - Details: SPA를 위한 Session/CSRF 또는 JWT 로그인 흐름 검증.

**🟢 GREEN: Implement to Make Tests Pass**
- [ ] **Task 1.3**: CORS 및 CSRF 설정 업데이트
  - File(s): `CTFd/__init__.py`, `CTFd/utils/security/csrf.py`
  - Details: 로컬 React 개발 서버(localhost:3000) 등에서의 접근 허용.

- [ ] **Task 1.4**: 모든 View 함수의 API화 (메인, 스코어보드, 문제 목록)
  - File(s): `CTFd/views.py`, `CTFd/api/v1/*.py`
  - Goal: `render_template`을 사용하는 모든 레거시 라우트를 제거하거나 API 블루프린트로 마이그레이션.
  - Note: 기존 `/api/v1`이 커버하지 못하는 데이터(예: 테마 설정, 서버 시간 등)를 노출하는 엔드포인트 추가.

**🔵 REFACTOR: Clean Up Code**
- [ ] **Task 1.5**: 템플릿 엔진 의존성 제거
  - Files: `CTFd/themes/core/templates/` 삭제 또는 아카이빙.
  - Goal: 백엔드가 더 이상 View Layer에 관여하지 않음을 보장.

#### Quality Gate ✋
- [ ] **Backend Unit Tests**: 100% Pass
- [ ] **API Coverage**: 프론트엔드 구현에 필요한 모든 액션(로그인, 조회, 제출)에 대응하는 API 존재 확인.
- [ ] **Swagger/OpenAPI Spec**: API 명세서 초안 작성 (프론트엔드 개발자용).

---

### Phase 2: Frontend Implementation (React)
**Goal**: 완성된 백엔드 API를 기반으로 React 프론트엔드 애플리케이션을 구현합니다.
**Estimated Time**: 40 hours
**Status**: ⏳ Pending

#### Tasks

**🔴 RED: Write Failing Tests First**
- [ ] **Test 2.1**: API 클라이언트 모킹 테스트
  - File(s): `frontend/src/__tests__/api.test.ts`
  - Details: 백엔드 API 스펙 기반으로 Mock Service Worker(MSW) 설정 및 응답 처리 테스트.

**🟢 GREEN: Implement to Make Tests Pass**
- [ ] **Task 2.2**: React 프로젝트 초기화 및 라우팅 설정
  - Details: Phase 1에서 정의된 API 명세를 바탕으로 `React Router` 구성.
- [ ] **Task 2.3**: 인증(Auth) 컴포넌트 및 Context 구현
  - Details: 로그인 여부에 따른 Protected Route 구현.
- [ ] **Task 2.4**: 핵심 페이지 구현 (대시보드, 문제, 스코어보드)
  - Details: 백엔드 API를 호출하여 데이터 바인딩.

**🔵 REFACTOR: Clean Up Code**
- [ ] **Task 2.5**: UI 디자인 고도화 (TailwindCSS)
  - Details: 기능 구현 후 UI 폴리싱 진행.

#### Quality Gate ✋
- [ ] **Frontend Build**: 에러 없이 빌드 성공.
- [ ] **Integration Check**: 실제 백엔드와 연동하여 주요 시나리오(로그인->문제풀이) 성공.

---

### Phase 3: Infrastructure Update (Docker)
**Goal**: Docker Compose를 수정하여 백엔드와 프론트엔드 컨테이너 분리.
**Estimated Time**: 10 hours
**Status**: ⏳ Pending

#### Tasks

- [ ] **Task 3.1**: Frontend Dockerfile 작성
  - File(s): `frontend/Dockerfile`
- [ ] **Task 3.2**: docker-compose.yml 수정
  - File(s): `docker-compose.yml`
  - Details: `ctfd` 서비스(API)와 `client` 서비스(React/Nginx) 정의.

#### Quality Gate ✋
- [ ] `docker-compose up` 실행 시 두 컨테이너 모두 정상 구동
- [ ] 브라우저에서 `localhost` 접속 시 React 앱 로드

---

## ⚠️ Risk Assessment

| Risk | Probability | Impact | Mitigation Strategy |
|------|-------------|--------|---------------------|
| API 문서 부재로 인한 개발 지연 | High | High | Swagger/OpenAPI 도입으로 API 명세 자동화 |
| 기존 테마 시스템 호환성 파괴 | High | High | 기존 테마 사용자에게 마이그레이션 가이드 제공 (이번 커스텀은 완전한 신규 UI이므로 호환성 포기 결정 필요) |

---

## 🔄 Rollback Strategy

### If Phase 1 Fails
**Steps to revert**:
- `git revert`로 백엔드 코드 원복.
- `docker-compose.yml` 원복.

---

## 📊 Progress Tracking

### Completion Status
- **Phase 1**: ⏳ 0%
- **Phase 2**: ⏳ 0%
- **Phase 3**: ⏳ 0%

**Overall Progress**: 0% complete

---

## 📝 Notes & Learnings
- (작성 예정)

---

## ✅ Final Checklist
- [ ] Frontend와 Backend가 완전히 분리되었는가?
- [ ] 모든 주요 기능(로그인, 문제 조회)이 React에서 작동하는가?
