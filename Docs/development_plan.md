# Zibro 서비스 개발 기획안 (v2)

## 1. 개요

이 문서는 'Zibro' 서비스 개발을 위한 단계별 계획을 정의합니다. **배송 요청자(고객)는 점주 QR 스캔을 통해 진입하는 웹페이지에서 정보를 등록하고, 이후 카카오톡 알림에 포함된 웹페이지 링크로 배송 현황을 추적**합니다. 점주와 배달기사는 별도의 Flutter 앱을 사용하는 기획안을 기준으로 구체적인 실행 방안을 기술합니다.

- **백엔드 기술 스택(안):** Node.js (NestJS), PostgreSQL
- **클라이언트 기술 스택:** Flutter (상점/기사용), **Web (고객용)**, 카카오톡 챗봇
- **주요 개발 플랫폼:** iOS, Android, Web, 카카오톡 채널

## 1.1. 서비스 이용 흐름

### 1. 고객 (Web)
- '점주 전용 QR' 스캔하여 웹페이지로 이동
- 이름, 주소(배송지), 전화번호 입력 및 개인정보 수집 동의

### 2. 점주 (Flutter App)
- **고객 응대:**
    1. 고객 전화번호 입력
    2. 고객 정보(이름, 주소) 확인
    3. '확인' 버튼 선택 시 배송 건 자동 등록 및 고유번호 출력 (예: "해당 배송 건의 고유번호는 1123 입니다.")
    4. 등록 완료 후, 고객에게 카카오톡 알림 발송 (실시간 배송 현황을 확인할 수 있는 웹페이지 링크 포함)
- **라이더 응대:**
    1. QR 코드 스캔 화면을 통해 라이더의 QR 코드 스캔
    2. 라이더 도착 확인 및 배송 건 이관 알림 팝업 표출

### 3. 라이더 (Flutter App)
- **배송 건 선택:**
    1. 현 위치 반경 3km 이내의 마트 배송 건 리스트 확인 (배송 단가, 배송지 위치 정보 포함)
    2. 'Pick' 버튼을 선택하여 배달 진행 (최대 3건)
- **픽업 및 배송:**
    1. 마트 도착 후, 라이더 앱의 QR 코드를 점주에게 제시
    2. 점주가 스캔하면 배송할 물품 목록(고유번호 포함) 확인
    3. 고유번호를 보고 마트에서 물품 픽업
    4. 배송 시작 및 완료 처리 (관련 UI 필요)

---

## 2. 개발 단계 (Phase)

### Phase 1: 프로젝트 기반 설정 및 데이터베이스 설계 (1주차)

**목표:** 개발 환경을 구축하고, 변경된 서비스의 근간이 될 데이터베이스 스키마를 확정합니다.

1.  **프로젝트 환경 설정** (기존과 동일)
    *   Git Repository 생성 및 브랜치 전략 수립.
    *   백엔드, Flutter 프로젝트 초기화 및 기본 폴더 구조 설정.
    *   개발, 스테이징, 프로덕션 환경 변수 관리 방안 수립.

2.  **데이터베이스(PostgreSQL) 스키마 설계**
    *   ERD(Entity-Relationship Diagram) 작성.
    *   **카카오톡 챗봇 연동을 고려하여 사용자 식별 방식을 전화번호 중심으로 변경.**

    ```sql
    -- 사용자 (상점, 기사) - 이메일/비밀번호로 로그인
    CREATE TABLE users (
        id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
        email VARCHAR(255) UNIQUE NOT NULL,
        password_hash VARCHAR(255) NOT NULL,
        user_type VARCHAR(20) NOT NULL CHECK (user_type IN ('store', 'driver')), -- 사용자 유형
        created_at TIMESTAMPTZ DEFAULT now(),
        updated_at TIMESTAMPTZ DEFAULT now()
    );

    -- 배송 요청자 프로필 (고객) - 앱이 없으므로 전화번호로 식별
    CREATE TABLE customers (
        id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
        name VARCHAR(100) NOT NULL,
        phone_number VARCHAR(20) UNIQUE NOT NULL, -- Primary Identifier
        address TEXT,
        created_at TIMESTAMPTZ DEFAULT now(),
        updated_at TIMESTAMPTZ DEFAULT now()
    );

    -- 상점 프로필
    CREATE TABLE store_profiles (
        user_id UUID PRIMARY KEY REFERENCES users(id),
        store_name VARCHAR(255) NOT NULL,
        address TEXT NOT NULL,
        business_registration_number VARCHAR(100) UNIQUE NOT NULL
    );

    -- 배달기사 프로필
    CREATE TABLE driver_profiles (
        user_id UUID PRIMARY KEY REFERENCES users(id),
        name VARCHAR(100) NOT NULL,
        phone_number VARCHAR(20) UNIQUE NOT NULL,
        is_available BOOLEAN DEFAULT false -- 업무 시작/종료 상태
    );

    -- 배송 (핵심 데이터)
    CREATE TABLE deliveries (
        id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
        customer_id UUID NOT NULL REFERENCES customers(id),
        store_id UUID NOT NULL REFERENCES users(id),
        driver_id UUID REFERENCES users(id),
        status VARCHAR(50) NOT NULL DEFAULT 'pending', -- 배송 상태 (e.g., pending, assigned, picked_up, completed)
        destination_address TEXT NOT NULL,
        fee NUMERIC(10, 2) NOT NULL,
        items_description TEXT,
        completion_photo_url TEXT,
        created_at TIMESTAMPTZ DEFAULT now(),
        accepted_at TIMESTAMPTZ,
        picked_up_at TIMESTAMPTZ,
        completed_at TIMESTAMPTZ
    );
    ```

### Phase 2: 핵심 API 개발 (2-3주차)

**목표:** 상점/기사 앱 및 카카오톡 챗봇의 핵심 기능을 처리하는 백엔드 API를 구현합니다.

1.  **사용자 인증 API (상점/기사용)**
    *   `POST /api/auth/register`: 상점/기사 회원가입
    *   `POST /api/auth/login`: 상점/기사 로그인 (JWT 토큰 발급)
    *   `GET /api/users/me`: 내 정보 조회 (상점/기사용)

2.  **고객 관리 및 배송 접수 API (상점용)**
    *   `GET /api/stores/customers/lookup`: 전화번호로 고객 정보 조회.
    *   `POST /api/stores/customers`: 신규 고객 등록 (이름, 주소, 전화번호).
    *   `POST /api/stores/deliveries`: 신규 배송 건 등록 (고객 ID, 배송 정보 포함).

3.  **배송 관리 API (기사/상점/챗봇 연동)**
    *   `GET /api/deliveries/new`: **(기사용)** 신규 배송 목록 조회.
    *   `PATCH /api/deliveries/:id/accept`: **(기사용)** 배송 수락.
    *   `POST /api/stores/handoff`: **(상점용)** 라이더 QR 스캔 시, 해당 라이더에게 배정된 배송 건들을 '픽업' 상태로 일괄 변경.
    *   `POST /api/deliveries/:id/complete`: **(기사용)** 배송 완료 (사진 업로드 포함).
    *   `GET /api/deliveries`: **(상점/기사용)** 자신의 배송 내역 목록 조회.
    *   `GET /api/deliveries/:id`: 배송 건 상세 정보 조회.

4.  **카카오톡 챗봇 연동 API**
    *   `POST /api/kakao/webhook`: 카카오톡 챗봇의 모든 요청을 수신하는 웹훅.
        *   (Request Body 분석하여 내 배송현황, 정보변경 등 시나리오 분기 처리)
    *   `POST /api/kakao/send-notification`: 카카오톡 친구톡/알림톡 발송을 위한 내부 API.
        *   (배송 상태 변경 시, 이 API를 호출하여 고객에게 알림)

### Phase 3: 클라이언트 개발 (3-5주차)

**목표:** 2개의 Flutter 앱(상점, 기사), 고객용 웹페이지, 카카오톡 챗봇의 화면 및 기능을 개발합니다.

1.  **공통 모듈 개발 (Flutter)**
    *   로그인, 회원가입, API 클라이언트, 상태 관리 등 (기존 계획과 유사)

2.  **카카오톡 챗봇 개발**
    *   **챗봇 시나리오 1: 배송 상태 확인**
        *   전화번호 입력 -> 진행 중인 배송 목록 표시 -> 상세 보기 선택 -> 현재 상태와 기사 정보 안내
    *   **챗봇 시나리오 2: 내 정보(배송지) 변경**
        *   전화번호 인증 -> 주소 입력 -> 주소 변경 API 호출
    *   **알림톡 연동:** 배송 접수, 기사 배정, 픽업, 배송 완료 등 주요 단계마다 고객에게 알림톡 발송.

3.  **배송필요 업체 (상점) 앱 개발**
    *   **메인:** 배송 접수 버튼, 금일 현황 요약.
    *   **고객 조회/등록:** 전화번호로 고객 조회, 없을 시 신규 등록 (이름, 주소, 연락처).
    *   **배송 접수:** 조회/등록된 고객 정보와 물품 정보를 합쳐 배송 요청.
    *   **배송 관리:** 실시간 배송 목록 및 상태 관리.
    *   **정산 및 통계:** 기간별 수수료 정산 내역.

4.  **배달기사 앱 개발** (기존 계획과 대부분 동일)
    *   **메인 대시보드:** 업무 시작/종료, 배정된 배송 목록.
    *   **신규 배송 목록:** 신규 배송 건 수락.
    *   **배송 수행:** 픽업/완료 상태 업데이트, 완료 사진 촬영 및 업로드.
    *   **기타:** 수익 및 운행 기록 확인.

5.  **고객용 웹페이지 개발 (Web)**
    *   **배송 정보 등록 페이지:** 점주 QR 스캔 시 진입, 고객 이름/주소/전화번호 입력 및 개인정보 동의.
    *   **실시간 배송 조회 페이지:** 카카오톡 알림톡 내 링크를 통해 접근, 배송 상태 및 기사 위치(예정) 확인.

### Phase 4: 통합, 테스트 및 배포 (6주차)

**목표:** 전체 시스템(앱, 챗봇, 서버)을 통합하고, 테스트를 통해 안정성을 확보하여 배포합니다.

1.  **시스템 통합 테스트**
    *   개발된 모든 API를 Flutter 앱과 카카오톡 챗봇 시나리오에 연동.
    *   실제 데이터 흐름 테스트 및 디버깅.

2.  **엔드-투-엔드(E2E) 테스트**
    *   시나리오 기반 테스트: (상점)고객등록/배송접수 -> (카톡)고객알림 -> (기사)배차/픽업 -> (카톡)고객알림 -> (기사)완료 -> (카톡)고객알림
    *   전체 플로우 테스트.

3.  **배포 준비**
    *   **백엔드:** Docker 이미지 생성, 클라우드 플랫폼(AWS 등)에 배포.
    *   **프론트엔드:** 고객용 웹페이지 배포, iOS/Android 앱 빌드 및 스토어 등록 준비.
    *   **챗봇:** 카카오톡 채널에 챗봇 시나리오 최종 배포.

---

## 3. 향후 고려사항

*   **결제 시스템 연동:** (기획 구체화 필요)
*   **고급 기능:** 묶음 배송 로직, 최적 경로 계산 알고리즘 고도화.
*   **데이터 분석:** 수집된 배송 데이터를 활용한 통계 및 예측 기능.

