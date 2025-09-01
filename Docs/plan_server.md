# 서버 개발 상세 계획서 (plan_server.md)

이 문서는 Zibro 서비스의 백엔드(Server) 개발을 위한 상세 작업 계획을 정의합니다.

---

## Phase 1: 프로젝트 설정 및 데이터베이스 구축 (1주차)

**목표:** 안정적인 서비스 운영을 위한 서버 환경과 데이터베이스 스키마를 구축합니다.

- **Step 1.1: 백엔드 프로젝트 초기화**
  - **Task 1.1.1:** NestJS 프로젝트 생성 (`nest new zibro-server`)
  - **Task 1.1.2:** 폴더 구조 설계 (`src/modules`, `src/core`, `src/common`)
  - **Task 1.1.3:** TypeORM 모듈 설치 및 데이터베이스 연결 설정 (`ormconfig.json`)

- **Step 1.2: 데이터베이스 마이그레이션 실행**
  - **Task 1.2.1:** `development_plan.md` 기반으로 TypeORM 엔티티(Entity) 클래스 작성 (User, Profile, Delivery 등)
  - **Task 1.2.2:** 최초 마이그레이션 파일 생성 (`typeorm migration:generate`)
  - **Task 1.2.3:** 마이그레이션 스크립트 실행하여 개발 DB에 테이블 생성 (`typeorm migration:run`)

- **Step 1.3: 로깅 및 환경변수 설정**
  - **Task 1.3.1:** `ConfigModule`을 사용하여 `.env` 환경변수 관리 설정
  - **Task 1.3.2:** `winston` 라이브러리 설치 및 로거 모듈 생성
  - **Task 1.3.3:** 요청/응답 로깅을 위한 미들웨어 작성

---

## Phase 2: 핵심 API 개발 (2-3주차)

**목표:** 프론트엔드와 통신하며 서비스의 핵심 비즈니스 로직을 처리하는 API를 개발합니다.

- **Step 2.1: 사용자 인증 모듈 개발 (상점/기사용)**
  - **Task 2.1.1:** `AuthModule` 생성 및 `bcrypt`, `PassportModule`, `JwtModule` 설정
  - **Task 2.1.2:** `POST /api/auth/register` API 구현 (사용자/프로필 생성)
  - **Task 2.1.3:** `POST /api/auth/login` API 구현 (JWT 발급)
  - **Task 2.1.4:** 인증 가드(`JwtAuthGuard`)를 사용한 `GET /api/users/me` API 구현

- **Step 2.2: 고객 관리 및 배송 접수 모듈 개발 (상점용)**
  - **Task 2.2.1:** `CustomerModule` 및 `StoreModule` 생성
  - **Task 2.2.2:** `GET /api/stores/customers/lookup` API 구현 (전화번호로 고객 조회)
  - **Task 2.2.3:** `POST /api/stores/customers` API 구현 (신규 고객 등록)
  - **Task 2.2.4:** `POST /api/stores/deliveries` API 구현 (신규 배송 건 등록)

- **Step 2.3: 배송 관리 모듈 개발 (기사/상점/챗봇 연동)**
  - **Task 2.3.1:** `DeliveryModule` 생성
  - **Task 2.3.2:** `GET /api/deliveries/new` API 구현 (기사용 신규 배송 목록 조회)
  - **Task 2.3.3:** `PATCH /api/deliveries/:id/accept` API 구현 (기사의 배송 수락)
  - **Task 2.3.4:** `POST /api/stores/handoff` API 구현 (라이더 QR 스캔에 따른 픽업 상태 일괄 변경)
  - **Task 2.3.5:** AWS S3 연동 및 파일 업로드 서비스 구현 (`Multer`)
  - **Task 2.3.6:** `POST /api/deliveries/:id/complete` API 구현 (완료 사진 업로드 포함)
  - **Task 2.3.7:** `GET /api/deliveries` 및 `GET /api/deliveries/:id` 구현 (배송 내역/상세 조회)

---

## Phase 3: 고급 기능 및 통합 (4주차)

**목표:** 서비스의 안정성을 높이고, 푸시 알림 등 부가 기능을 구현합니다.

- **Step 3.1: 푸시 알림 시스템 개발**
  - **Task 3.1.1:** `node-fcm` 등 라이브러리 설치 및 Firebase 연동 설정
  - **Task 3.1.2:** `NotificationService` 생성 (사용자 기기 토큰 관리, 푸시 발송 로직)
  - **Task 3.1.3:** 배송 상태 변경 등 주요 비즈니스 로직에 푸시 알림 발송 코드 통합

- **Step 3.2: API 문서화 및 테스트**
  - **Task 3.2.1:** `SwaggerModule` 설정하여 API 문서 자동화
  - **Task 3.2.2:** `Jest`를 이용한 `AuthService`, `DeliveryService` 단위 테스트 작성
  - **Task 3.2.3:** `supertest`를 이용한 주요 API 엔드포인트 통합 테스트 작성

- **Step 3.3: 카카오톡 챗봇 연동**
  - **Task 3.3.1:** `POST /kakao/webhook` 엔드포인트 구현
  - **Task 3.3.2:** 카카오톡 챗봇 시나리오에 따른 비즈니스 로직 분기 처리
  - **Task 3.3.3:** 카카오톡 알림톡/친구톡 발송을 위한 `POST /kakao/send-notification` API 구현

---

## Phase 4: 배포 및 운영 준비 (5-6주차)

**목표:** 개발된 서버 애플리케이션을 클라우드에 배포하고, 운영을 위한 준비를 마칩니다.

- **Step 4.1: Docker 및 배포 스크립트**
  - **Task 4.1.1:** Production용 `Dockerfile` 최적화 (Multi-stage build)
  - **Task 4.1.2:** ECR 푸시 및 ECS 배포를 위한 쉘 스크립트 작성

- **Step 4.2: 최종 점검**
  - **Task 4.2.1:** API 엔드포인트별 권한(인가) 로직 최종 검토
  - **Task 4.2.2:** `helmet`, `cors` 등 보안 관련 미들웨어 설정 검토
  - **Task 4.2.3:** 데이터베이스 인덱싱(Indexing) 전략 수립 및 적용


## Phase 3: 고급 기능 및 통합 (4주차)

**목표:** 서비스의 안정성을 높이고, 푸시 알림 등 부가 기능을 구현합니다.

- **Step 3.1: 푸시 알림 시스템 개발**
  - **Task 3.1.1:** `node-fcm` 등 라이브러리 설치 및 Firebase 연동 설정
  - **Task 3.1.2:** `NotificationService` 생성 (사용자 기기 토큰 관리, 푸시 발송 로직)
  - **Task 3.1.3:** 배송 상태 변경 등 주요 비즈니스 로직에 푸시 알림 발송 코드 통합

- **Step 3.2: API 문서화 및 테스트**
  - **Task 3.2.1:** `SwaggerModule` 설정하여 API 문서 자동화
  - **Task 3.2.2:** `Jest`를 이용한 `AuthService`, `DeliveryService` 단위 테스트 작성
  - **Task 3.2.3:** `supertest`를 이용한 주요 API 엔드포인트 통합 테스트 작성