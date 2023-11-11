# Docker CS

## 1. Docker와 Docker-Compose의 차이점은?
둘 다 애플리케이션을 컨테이너화 하지만, 몇 가지 차이점이 있음.

* Docker
  * 애플리케이션과 그 의존성을 컨테이너로 패키징하여, 어디에서나 동일하게 실행할 수 있도록 하는 플랫폼
  * 개별 컨테이너의 생성, 실행, 관리 목적
  * 명령어를 사용하여 컨테이너를 수동 관리 및 구성
  * 단일 컨테이너 또는 간단한 애플리케이션에 적합
* Docker Compose
  * 다중 컨테이너 Docker 애플리케이션을 정의하고 실행하기 위한 도구
  * YAML 파일을 사용하여 서비스, 네트워크, 볼륨 등 구성
  * 여러 컨테이너로 구성된 애플리케이션의 서비스를 한번에 정의하고 관리하기 때문에 MSA에서 주로 사용
  * 서비스 인스턴스 자동 스케일링
  * 복잡한 애플리케이션과 여러 서비스를 관리하기에 더 적합

## 2. Docker Hub는 뭘까요??
Docker 이미지를 저장하고 공유할 수 있는 클라우드 기반의 레지스트리 서비스

* Docker 이미지를 업로드하고 저장할 수 있으며, 다른 사용자가 공유한 이미지를 검색하고 다운로드할 수도 있음, 즉 도커 이미지의 GitHub 버전임
* 이미지 버전 관리 가능
* 웹 인터페이스 또는 CLI 인터페이스 제공

## 3. EC2 & Docker 사용하여 배포 시 어떤 프로세스로 처리될까?
* Docker File 생성
  * 애플리케이션을 실행하기 위한 모든 종속성과 실행 가능한 JAR 파일을 포함하는 이미지 빌드 방법 정의
* Docker image build
  * docker build 명령 -> Docker 이미지 빌드
* Docker Hub Image Push
  * 빌드한 이미지를 Docker Hub에 push
  * Docker Hub 로그인 & docker push command 통해서 image push
* EC2 Instance 구성 및 Docker Image pull
  * EC2 인스턴스 구성 & Docker 설치
  * docker pull 명령 사용하여 Docker Hub에서 이미지 pull
* 컨테이너 실행
  * docker run 명령을 사용하여 이미지를 기반으로 컨테이너를 실행
  * 프로젝트 배포

## 4. EC2 & Docker 사용하여 배포 시 어떤 이점이 있을까?