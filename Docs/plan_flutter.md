# Flutter 개발 상세 계획서 (plan_flutter.md)

이 문서는 Zibro 서비스의 프론트엔드(Flutter) 개발을 위한 상세 작업 계획을 정의합니다.

---

## Phase 1: 프로젝트 설정 및 공통 모듈 개발 (1-2주차)

**목표:** 본격적인 기능 개발에 앞서, 모든 화면에서 사용될 기반을 마련합니다.

- **Step 1.1: Flutter 프로젝트 초기화 및 설정**
  - **Task 1.1.1:** 신규 Flutter 프로젝트 생성 및 `flutter_lints` 적용
  - **Task 1.1.2:** 폴더 구조 생성 (`lib/app`, `lib/common`, `lib/data`, `lib/features`)
  - **Task 1.1.3:** `provider` 및 `get_it` 등 상태 관리/DI 라이브러리 추가 및 설정
  - **Task 1.1.4:** `go_router` 라이브러리 추가 및 기본 라우팅 설정

- **Step 1.2: 코어 모듈 개발**
  - **Task 1.2.1:** `dio` 라이브러리 추가 및 API 클라이언트 클래스 구현 (인터셉터 포함)
  - **Task 1.2.2:** `flutter_secure_storage`를 사용한 JWT 토큰 저장소 클래스 구현
  - **Task 1.2.3:** 서버 API DTO(Data Transfer Object) 모델 클래스 파일 생성

- **Step 1.3: 공통 UI 개발**
  - **Task 1.3.1:** `AppTheme` 클래스에 공통 색상, `TextTheme` 정의
  - **Task 1.3.2:** 공통 위젯 구현 (`PrimaryButton`, `StyledAppBar`, `PopupDialog`)
  - **Task 1.3.3:** 온보딩, 로그인, 회원가입 화면 UI 레이아웃 구성
  - **Task 1.3.4:** 로그인/회원가입 `ViewModel`/`Provider` 구현 및 API 연동

---

## Phase 2: 사용자 유형별 핵심 기능 개발 (3-4주차)

**목표:** 2가지 사용자 유형(점주, 기사)의 핵심 플로우를 화면 단위로 상세히 개발하고 API를 연동합니다.

### 2.1. 배달기사 앱 개발
- **Task 2.1.1: 메인 대시보드**
  - 업무 시작/종료 스위치 UI 및 API 연동
  - 신규 배송(콜) 목록 UI 및 API 연동 (`GET /api/deliveries/new`)
- **Task 2.1.2: 배송 수락 및 픽업 플로우**
  - `google_maps_flutter`를 이용한 지도 뷰 구현 (선택)
  - 배송 건 수락('담기') API 연동 (`PATCH /api/deliveries/:id/accept`)
  - 픽업 목록(묶음 배송) UI 구현
- **Task 2.1.3: 배송 완료 플로우**
  - `image_picker`를 이용한 카메라/갤러리 접근 로직 구현
  - 배송 완료 API(`POST /api/deliveries/:id/complete`)에 사진 첨부하여 전송하는 로직 구현
- **Task 2.1.4: 내 QR 코드 표시 화면**
  - 점주에게 제시할 본인 식별용 QR 코드를 표시하는 화면 UI 구현

### 2.2. 배송필요 업체 (상점) 앱 개발
- **Task 2.2.1: 메인 및 고객 관리**
  - 금일 현황 대시보드 UI 및 API 연동
  - 전화번호로 고객 조회/등록 화면 UI 및 관련 API 연동 (`GET /api/stores/customers/lookup`, `POST /api/stores/customers`)
  - 신규 배송 건 등록 화면 UI 및 API 연동 (`POST /api/stores/deliveries`)
- **Task 2.2.2: 라이더 연동 및 배송 관리**
  - `qr_code_scanner`를 이용한 라이더 QR 스캐너 화면 구현
  - 스캔 성공 시, 라이더 정보를 담아 `POST /api/stores/handoff` API 호출하는 로직 구현
  - 실시간 배송 목록 관리 화면 UI 및 API 연동
- **Task 2.2.3: 정산 관리**
  - 기간별 정산 내역 화면 UI 및 API 연동

---

## Phase 3: 통합 및 고도화 (5주차)

**목표:** 개발된 모든 기능의 완성도를 높이고, 사용자 경험을 개선합니다.

- **Step 3.1: 푸시 알림 연동**
  - **Task 3.1.1:** `firebase_messaging` 초기 설정 및 권한 요청
  - **Task 3.1.2:** 포그라운드/백그라운드 알림 수신 및 처리 로직 구현
  - **Task 3.1.3:** 알림 클릭 시, 특정 화면으로 이동하는 로직 구현

- **Step 3.2: 최종 테스트 및 디버깅**
  - **Task 3.2.1:** 사용자 시나리오별 E2E 테스트 및 버그 수정
  - **Task 3.2.2:** 주요 기능에 대한 위젯 테스트 코드 작성
