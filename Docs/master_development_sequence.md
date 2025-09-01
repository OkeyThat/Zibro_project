# 마스터 개발 순서도 (Master Development Sequence) v2

이 문서는 Zibro 서비스의 성공적인 개발 및 출시를 위해 `plan_devops.md`, `plan_server.md`, `plan_flutter.md`, `plan_kakao_ui.md`의 모든 작업을 논리적인 순서에 따라 재구성한 통합 체크리스트입니다.

**각 주차별 목표를 달성하면 서비스가 점진적으로 완성되는 것을 확인할 수 있습니다.**

---

### **1주차: 프로젝트 기반 및 기획 확정 (Foundation & Finalized Planning)**

**목표:** 개발 환경을 구축하고, 모든 컴포넌트(서버, 앱, 웹, 챗봇)의 기본 프로젝트를 생성하며, UI/UX 기획을 확정합니다.

| 담당 | Task | 완료 |
|---|---|:---:|
| **DevOps** | **Task 1.1:** GitHub Repository 생성 및 Git-flow 브랜치 전략 수립 | ☐ |
| **DevOps** | **Task 1.2:** 백엔드 및 DB 로컬 환경 컨테이너화 (`Dockerfile`, `docker-compose.yml`) | ☐ |
| **서버** | **Task 1.3:** NestJS 프로젝트 생성, TypeORM 초기 설정 및 DB 마이그레이션 실행 | ☐ |
| **Flutter** | **Task 1.4:** Flutter 프로젝트 생성 및 공통 모듈/UI 개발 (API 클라이언트, 테마, 공용 위젯 등) | ☐ |
| **Web** | **Task 1.5:** 고객용 웹 프로젝트 생성 (React/Vue 등) 및 기본 설정 | ☐ |
| **Kakao** | **Task 1.6:** 카카오톡 알림톡/챗봇 메시지 템플릿 및 기본 정책 수립 | ☐ |
| **DevOps** | **Task 1.7:** `README.md`에 로컬 환경 실행 가이드 작성 | ☐ |

---

### **2주차: 핵심 API 개발 및 CI/CD 파이프라인 구축 (Core APIs & CI/CD)**

**목표:** 앱/웹 클라이언트가 의존하는 핵심 API 개발을 완료하고, 모든 컴포넌트의 CI/CD 파이프라인을 구축하여 자동화 기반을 마련합니다.

| 담당 | Task | 완료 |
|---|---|:---:|
| **서버** | **Task 2.1:** 사용자 인증 API 개발 (`/auth/register`, `/login`, `/users/me`) | ☐ |
| **서버** | **Task 2.2:** 고객 관리 및 배송 접수 API 개발 (`/stores/customers`, `/stores/deliveries`) | ☐ |
| **서버** | **Task 2.3:** 배송 관리 API 개발 (`/deliveries/new`, `/accept`, `/complete`, `/stores/handoff`) | ☐ |
| **서버** | **Task 2.4:** 카카오톡 연동 API 개발 (`/kakao/webhook`, `/kakao/send-notification`) | ☐ |
| **DevOps** | **Task 2.5:** 백엔드 CI/CD 파이프라인 구축 (빌드, 테스트, ECR 푸시) | ☐ |
| **DevOps** | **Task 2.6:** Flutter 및 Web CI/CD 파이프라인 구축 (빌드, 테스트, 정적 파일 업로드) | ☐ |
| **Kakao** | **Task 2.7:** 챗봇 시나리오 및 고객용 웹페이지 화면 상세 기획 | ☐ |

---

### **3주차: 클라이언트 기능 구현 및 Staging 배포 (Client Implementation & Staging)**

**목표:** Staging 환경을 구축하고, 개발된 API를 사용하여 앱/웹의 핵심 기능을 구현 및 배포합니다.

| 담당 | Task | 완료 |
|---|---|:---:|
| **DevOps** | **Task 3.1:** AWS에 Staging용 인프라 구축 (VPC, RDS, ECS, S3, CloudFront) | ☐ |
| **DevOps** | **Task 3.2:** Staging 환경에 백엔드 및 웹 프론트엔드 자동 배포 설정 | ☐ |
| **Flutter** | **Task 3.3:** (점주용 앱) 고객 관리, 배송 접수, 라이더 QR 스캔 기능 구현 | ☐ |
| **Flutter** | **Task 3.4:** (기사용 앱) 신규 배송 목록 조회, 수락, 완료, 내 QR 코드 표시 기능 구현 | ☐ |
| **Web** | **Task 3.5:** (고객용 웹) 배송 정보 등록 및 실시간 조회 페이지 기능 구현 | ☐ |
| **서버** | **Task 3.6:** Swagger API 문서 최종 정리 및 클라이언트 팀에 공유 | ☐ |

---

### **4주차: 통합 테스트 및 고급 기능 구현 (Integration & Advanced Features)**

**목표:** 모든 컴포넌트를 Staging 환경에서 연동하여 테스트하고, 푸시 알림 등 서비스 완성도를 높이는 기능을 추가합니다.

| 담당 | Task | 완료 |
|---|---|:---:|
| **전체** | **Task 4.1:** Staging 환경에서 E2E 시나리오 테스트 및 발견된 버그 수정 | ☐ |
| **서버** | **Task 4.2:** 푸시 알림 시스템(FCM) 연동 및 발송 로직 구현 | ☐ |
| **Flutter** | **Task 4.3:** `firebase_messaging` 연동 및 푸시 알림 수신/처리 로직 구현 | ☐ |
| **서버** | **Task 4.4:** 주요 서비스 로직에 대한 단위/통합 테스트 코드 작성 | ☐ |
| **Kakao** | **Task 4.5:** 카카오 채널에 알림톡 템플릿 등록/검수 및 챗봇 시나리오 최종 검토 | ☐ |

---

### **5주차: Production 배포 및 최종 검증 (Production Deployment)**

**목표:** 실제 서비스 환경을 구축하고, 모든 애플리케이션을 배포하여 출시 직전 최종 검증을 완료합니다.

| 담당 | Task | 완료 |
|---|---|:---:|
| **DevOps** | **Task 5.1:** Production용 클라우드 인프라(DB, S3, ECS 등) 및 네트워크 환경 구축 | ☐ |
| **DevOps** | **Task 5.2:** Production용 도메인(DNS) 및 SSL/TLS 인증서 설정 | ☐ |
| **DevOps** | **Task 5.3:** Production 서버에 백엔드 및 웹 프론트엔드 최종 버전 배포 | ☐ |
| **전체** | **Task 5.4:** Production 환경에서 최종 E2E 테스트 수행 | ☐ |
| **Flutter** | **Task 5.5:** 최종 프로덕션 빌드(AAB/IPA) 생성 | ☐ |
| **DevOps** | **Task 5.6:** Google Play Console, App Store Connect에 앱 정보 등록 및 최종 앱 제출 | ☐ |

---

### **6주차 이후: 운영 및 모니터링 (Operation & Monitoring)**

**목표:** 서비스 출시 이후, 안정적인 운영을 위한 시스템을 구축하고 사용자 피드백을 수집합니다.

| 담당 | Task | 완료 |
|---|---|:---:|
| **DevOps** | **Task 6.1:** Sentry 에러 트래킹 시스템 적용 (서버, Flutter, Web) | ☐ |
| **DevOps** | **Task 6.2:** CloudWatch 대시보드 설정 및 주요 지표에 대한 경보(Alarm) 설정 | ☐ |
| **전체** | **Task 6.3:** 사용자 피드백 및 운영 데이터 기반으로 개선사항 도출 및 다음 스프린트 계획 | ☐ |
