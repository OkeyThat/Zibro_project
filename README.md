# Zibro Zerone (집으로 제로원) 프로젝트

통합 배송 관리 및 고객 커뮤니케이션 서비스 'Zibro Zerone'의 공식 레포지토리입니다.

## 🚀 로컬 개발 환경 실행 가이드

이 가이드를 따라 백엔드 서버와 프론트엔드 앱을 로컬 환경에서 실행할 수 있습니다.

### 사전 준비 사항

- [Docker](https://www.docker.com/products/docker-desktop/)
- [Flutter SDK](https://docs.flutter.dev/get-started/install)

---

### 1. 백엔드 서버 실행하기

NestJS와 PostgreSQL 데이터베이스를 Docker 컨테이너로 실행합니다.

1.  **서버 디렉토리로 이동합니다.**
    ```bash
    cd server
    ```

2.  **Docker Compose를 사용하여 컨테이너를 빌드하고 실행합니다.**
    ```bash
    docker-compose up --build
    ```

> 서버는 `http://localhost:3000`에서 실행됩니다.

---

### 2. 프론트엔드 앱 실행하기

Flutter 웹 앱을 Chrome 브라우저에서 실행합니다.

1.  **앱 디렉토리로 이동합니다.**
    ```bash
    cd app
    ```

2.  **Flutter 개발 서버를 실행합니다.**
    ```bash
    flutter run -d chrome
    ```
    > **참고:** 터미널에서 `flutter` 명령어를 찾을 수 없다는 메시지가 나오면, 셸 설정 파일(`.zshrc` 등)에 Flutter 경로가 올바르게 추가되었는지 확인하세요.

> 앱이 Chrome의 새 탭에서 자동으로 열립니다. 코드 변경 시 핫 리로드(Hot Reload)가 지원됩니다.
