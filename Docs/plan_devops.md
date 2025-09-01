# DevOps 및 인프라 상세 구축 계획서 (plan_devops.md)

이 문서는 Zibro 서비스의 전체 개발/운영 환경 설정, CI/CD 파이프라인, 배포 및 모니터링 전략을 상세히 정의합니다.

---

## Phase 1: 개발 환경 및 버전 관리 설정 (1주차)

**목표:** 모든 개발자가 동일한 환경에서 효율적으로 협업할 수 있는 기반을 마련합니다.

- **Step 1.1: Git Repository 및 브랜치 전략 수립**
  - **Task 1.1.1:** GitHub Repository 생성 (백엔드, 프론트엔드 Repo 분리 또는 Monorepo)
  - **Task 1.1.2:** `Git-flow` 브랜치 전략 (`main`, `develop`, `feature/*` 등) 문서화 및 팀 공유
  - **Task 1.1.3:** 브랜치 보호 규칙(Branch Protection Rule) 설정 (`main`, `develop` 브랜치)

- **Step 1.2: 로컬 개발 환경 컨테이너화**
  - **Task 1.2.1:** 백엔드용 `Dockerfile` 작성
  - **Task 1.2.2:** PostgreSQL 서비스 포함 `docker-compose.yml` 파일 작성
  - **Task 1.2.3:** 로컬 환경 실행을 위한 `.env.development` 파일 샘플 제공
  - **Task 1.2.4:** `README.md`에 로컬 환경 실행 가이드 작성

---

## Phase 2: CI/CD 파이프라인 구축 (2-3주차)

**목표:** 코드 변경 사항을 자동으로 테스트하고 빌드하여 배포 가능한 상태로 만드는 과정을 자동화합니다.

- **Step 2.1: 백엔드 CI/CD 파이프라인 (GitHub Actions)**
  - **Task 2.1.1:** `backend-ci.yml` 워크플로우 파일 생성
  - **Task 2.1.2:** `feature` 브랜치 push 시, `npm test` 실행하는 CI 잡(Job) 정의
  - **Task 2.1.3:** `develop` 브랜치 merge 시, Docker 이미지 빌드 및 ECR(Elastic Container Registry)에 푸시하는 CD 잡 정의
  - **Task 2.1.4:** ECR 푸시 성공 시, Staging 서버(ECS)에 신규 버전 배포를 트리거하는 잡 정의

- **Step 2.2: 프론트엔드(Flutter) CI/CD 파이프라인 (GitHub Actions)**
  - **Task 2.2.1:** `flutter-ci.yml` 워크플로우 파일 생성
  - **Task 2.2.2:** `feature` 브랜치 push 시, `flutter analyze` 및 `flutter test` 실행하는 CI 잡 정의
  - **Task 2.2.3:** `develop` 브랜치 merge 시, Staging용 APK/AAB 빌드 및 Firebase App Distribution에 업로드하는 CD 잡 정의
  - **Task 2.2.4:** `main` 브랜치 merge 시, Production용 빌드 및 스토어 출시 준비 잡 정의

- **Step 2.3: 프론트엔드(Web) CI/CD 파이프라인 (GitHub Actions)**
  - **Task 2.3.1:** `web-ci.yml` 워크플로우 파일 생성
  - **Task 2.3.2:** `feature` 브랜치 push 시, `npm test` 및 `lint` 실행하는 CI 잡 정의
  - **Task 2.3.3:** `develop` 브랜치 merge 시, 정적 웹 파일 빌드 및 Staging S3 버킷에 업로드하는 CD 잡 정의

---

## Phase 3: 클라우드 인프라 구축 및 배포 (4-5주차)

**목표:** Staging 및 Production 환경을 AWS에 구축하고, 애플리케이션을 배포합니다.

- **Step 3.1: 네트워크 및 백엔드 환경 구축 (AWS)**
  - **Task 3.1.1:** VPC, Public/Private Subnet, NAT Gateway 등 네트워크 인프라 생성 (Terraform 또는 AWS CDK 사용 권장)
  - **Task 3.1.2:** Staging/Production 용 RDS(PostgreSQL) 인스턴스 생성
  - **Task 3.1.3:** Staging/Production 용 S3 버킷 생성 (이미지 등 정적 파일 저장용)
  - **Task 3.1.4:** Staging/Production 용 ECS(Fargate) 클러스터 및 Task Definition 생성
  - **Task 3.1.5:** CI/CD 파이프라인과 연동하여 Staging/Production 서버에 백엔드 배포

- **Step 3.2: 고객용 웹 프론트엔드 인프라 구축 및 배포**
  - **Task 3.2.1:** Staging/Production 용 S3 버킷 생성 (정적 웹사이트 호스팅용)
  - **Task 3.2.2:** CloudFront 배포 생성 및 S3 버킷과 연결 (CDN, HTTPS 적용)
  - **Task 3.2.3:** Route 53을 사용한 도메인(zibro.com, app.zibro.com 등) 설정 및 CloudFront 연결
  - **Task 3.2.4:** CI/CD 파이프라인과 연동하여 웹 프론트엔드 배포 자동화

- **Step 3.3: 앱 스토어 배포**
  - **Task 3.3.1:** Google Play Console, Apple App Store Connect 설정 및 앱 정보 등록
  - **Task 3.3.2:** 스토어 정책에 맞는 개인정보처리방침 등 문서 준비
  - **Task 3.3.3:** 최종 빌드된 AAB/IPA 파일 업로드 및 심사 제출

---

## Phase 4: 모니터링 및 운영 (6주차+)

**목표:** 서비스 출시 이후, 안정적인 운영을 위한 모니터링 및 로깅 시스템을 구축합니다.

- **Step 4.1: 로깅 및 에러 트래킹 시스템 구축**
  - **Task 4.1.1:** `Sentry` SDK를 서버, Flutter 앱, 고객용 웹 앱에 적용
  - **Task 4.1.2:** Sentry를 통해 에러 및 성능 저하 이슈 트래킹

- **Step 4.2: 모니터링 및 알림 설정**
  - **Task 4.2.1:** AWS CloudWatch 대시보드 설정 (서버 CPU/Memory, RDS 연결 수, API Latency 등)
  - **Task 4.2.2:** 주요 지표에 대한 CloudWatch 경보(Alarm) 설정
  - **Task 4.2.3:** 경보 발생 시, 지정된 Slack 채널로 알림이 오도록 AWS Chatbot 연동
