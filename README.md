# SSH 자동 배포 프로젝트

이 프로젝트는 Spring Boot 애플리케이션을 Docker 컨테이너화하고, GitHub Actions와 SSH를 통해 AWS EC2에 자동으로 배포하는 환경을 구축한 예제입니다.

## 주요 구성 요소

### 1. Spring Boot 애플리케이션
- **버전 관리**: `src/main/resources/application.yaml`의 `app.version` 값을 사용합니다.
- **REST API**: `/` 경로로 접속 시 설정된 버전 정보를 응답합니다.

### 2. Docker 설정
- **Dockerfile**: 멀티 스테이징 빌드 방식을 사용하여 최종 이미지 크기를 최적화했습니다.
  - 1단계: Gradle을 이용한 애플리케이션 빌드
  - 2단계: JRE 환경에서 빌드된 jar 실행
- **Docker Compose**: 애플리케이션을 컨테이너로 실행하며, 호스트의 **80 포트**를 컨테이너의 **8080 포트**와 연결합니다.

### 3. CI/CD (GitHub Actions)
- **배포 플로우**: `main` 브랜치에 코드가 푸시되면 자동으로 실행됩니다.
- **SSH 배포**: PEM 키를 사용하여 AWS EC2에 접속합니다.
- **자동화 스크립트**:
  - 서버 내 배포 디렉토리 존재 여부를 확인합니다.
  - 디렉토리가 없으면 `git clone`을, 있으면 `git pull`을 수행합니다.
  - `docker-compose up --build -d` 명령어를 통해 이미지를 재빌드하고 컨테이너를 갱신합니다.

## 시작하기 전 설정 (GitHub Secrets)
GitHub 저장소의 `Settings > Secrets and variables > Actions`에 다음 항목을 반드시 설정해야 합니다.

| Secret 이름 | 설명 |
| :--- | :--- |
| `EC2_HOST` | AWS EC2 인스턴스의 퍼블릭 IP 또는 도메인 |
| `EC2_USERNAME` | SSH 접속 사용자 이름 (예: `ubuntu`, `ec2-user`) |
| `EC2_SSH_KEY` | EC2 접속을 위한 PEM 키 내용 전체 |

## 실행 방법
로컬 환경에서 Docker를 통해 실행하려면 다음 명령어를 사용하세요.
```bash
docker-compose up --build
```
접속 주소: `http://localhost`
