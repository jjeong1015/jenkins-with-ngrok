# 🚀 Jenkins와 Ngrok을 활용한 CI/CD 파이프라인 구축
Jenkins와 Ngrok을 활용한 CI/CD 파이프라인 구축 가이드이다. <br>
포트포워딩부터 Jenkins 설정까지 단계별로 설명하고 있으며, 관련 명령어와 사진을 포함해 따라 하기 쉽게 구성했다. <br>

## 기술 스택
<img src="https://img.shields.io/badge/VirtualBox-183A61?style=for-the-badge&logo=VirtualBox&logoColor=black"><img src="https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black"><img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=Docker&logoColor=black"><img src="https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=Jenkins&logoColor=white"><img src="https://img.shields.io/badge/Ngrok-1F1E37?style=for-the-badge&logo=ngrok&logoColor=white">

## 🎡 Jenkins란?
수동으로 진행해야 하는 소프트웨어 개발과 배포 과정에서 반복적인 작업을 **자동화**하는 데 최적화된 도구이다.

### ❓ 왜 Jenkins를 사용하는가?
빌드, 테스트, 배포를 자동화하여 개발 속도를 높일 수 있고, 자주 발생하는 코드 변경 사항을 빠르게 통합, 테스트, 배포하여 품질 관리와 배포 속도를 동시에 개선할 수 있기 때문에 사용한다.

<br>

## 🌐 Ngrok이란?
### 🔍 **Ngrok 개념**
Ngrok은 **내부 네트워크(Localhost)에서 실행 중인 웹 애플리케이션을 외부에서 접근할 수 있도록 돕는 터널링 도구**.  
즉, **로컬 환경에서 실행 중인 서버에 퍼블릭 URL을 부여**하여 외부에서도 접근 가능하도록 만들어준다.

### ✅ **Ngrok의 주요 기능**
1. **퍼블릭 URL 제공** → 로컬에서 실행되는 서비스를 외부에서 접근 가능하게 함
2. **포트포워딩 역할 수행** → 방화벽이나 NAT 뒤에서도 접근 가능
3. **Webhook 테스트 용이** → GitHub, Slack 등의 Webhook 이벤트를 로컬에서 테스트 가능

<br>

## 📌 Jenkins와 Ngrok을 함께 사용하는 이유
**Jenkins와 Ngrok을 함께 사용하는 이유**는 로컬 환경에서 실행 중인 Jenkins 서버를 **외부에서 접근 가능하도록 만들기 위해서**이다.  
Jenkins는 기본적으로 **로컬에서만 접근 가능**하지만, GitHub Webhook과 연동하려면 **외부에서도 Jenkins 서버에 접근할 수 있어야 한다**.  
Ngrok을 활용하면 **고정된 퍼블릭 URL을 제공하여 GitHub Webhook이 Jenkins 서버에 이벤트를 전달할 수 있도록 설정**할 수 있다.

<br>

## ⚙️ **Jenkins + Ngrok을 활용한 CI/CD 흐름**
1. 개발자가 GitHub에 코드를 **푸시(Push)** 하면, GitHub Webhook이 동작한다.
2. Webhook이 Jenkins의 특정 URL(`/github-webhook/`)을 호출하여 **빌드 트리거**를 실행한다.
3. 하지만 Jenkins가 로컬에서만 실행 중이라면 Webhook이 접근할 수 없다.
4. **Ngrok을 이용해 Jenkins 서버에 퍼블릭 URL을 부여**하면, Webhook이 해당 URL을 통해 접근 가능해진다.
5. Jenkins는 **빌드를 실행**하고, 새로운 코드가 자동으로 배포된다.

<br>

## 0. 포트포워딩
포트포워딩을 통해 외부에서 로컬 서버에 접근한다.
<br>![9999-2](https://github.com/user-attachments/assets/0b9c8f4d-d351-49a8-a253-e1720d2b35dc)

## 1. Ngrok
Ngrok을 활용해 로컬 환경을 외부에서 접근 가능하도록 설정한다.
```bash
# CMD
$ cd C:\02.devEnv
$ ngrok config add-authtoken YOUR_AUTHTOKEN
$ ngrok http http://localhost:8080
```
<!-- ngrok config add-authtoken 2mazkbTnosXgNxUn7GmF9DCltsR_c2eF9uiFGTPBtf3TxBqn -->

```bash
# CMD
$ ngrok http http://localhost:8888
```
Ngrok에서 제공된 URL을 GitHub Webhook의 Payload URL에 입력하고 /github-webhook/을 추가한다.
![5555](https://github.com/user-attachments/assets/f5b169b9-e956-4bc4-9d53-9b4ae238e89f)

## 2. Jenkins
Jenkins를 Docker 컨테이너로 설치하고 실행한다.
```bash
# Linux
# Docker로 Jenkins 설치 및 실행
$ docker run --name myjenkins --privileged -p 8888:8080 jenkins/jenkins:lts-jdk17

# 실행된 Jenkins 컨테이너 확인
$ docker ps

# jenkins가 실행되지 않았을 경우 jenkins 컨테이너 이름 실행
$ docker start myjenkins
```
### 2-1. Jenkins 접속 및 로그인
브라우저에서 localhost:8888에 접속하여 Jenkins에 로그인한다.
![1111](https://github.com/user-attachments/assets/56b9d046-4c72-4506-b5ee-ab88059ab3c3)
![6666](https://github.com/user-attachments/assets/78bdd879-5623-4bf7-90b4-f9a187cadfae)
<!-- 아이디 : admin, 비밀번호 : 59361ec075324f82aeb03d699a003e66 -->
![2222](https://github.com/user-attachments/assets/8d449ae9-14de-4c81-9955-21d2d4f23a39)

### 2-2. Jenkins에서 새로운 아이템 생성
#### Dashboard > All > New Item > Freestyle 프로젝트 선택 > Ok
![3333](https://github.com/user-attachments/assets/5d923774-85da-4874-a047-efc0f2880add)
 
#### Dashboard > item name > Configuration > Pipeline
```bash
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'main', url: 'https://github.com/jjeong1015/jenkinsTest.git'
            }
        }
    }
}
```
![4444](https://github.com/user-attachments/assets/c42ad86c-9b22-4082-8477-608f1f28cb8f)

### 2-3. Jenkins의 플러그인 관리에서 stage view 플러그인을 설치
Dashboard > Jenkins 관리 > Plugins > Available plugins > stage view 설치
![7777](https://github.com/user-attachments/assets/75a3c0ca-8b5f-4da6-af92-dc41f58e2b95)
![8888](https://github.com/user-attachments/assets/91b44c06-bfe7-4efe-9012-7a94a0fe9219)

### 2-4. Jenkins 빌드 트리거 설정
#### localhost:8888 > item name > 지금 빌드 버튼을 클릭해 수동으로 빌드를 실행한다.
```bash
# Linux
$ docker ps
# jenkins가 실행되지 않았을 경우 실행
$ docker start myjenkins
```
![9999-3](https://github.com/user-attachments/assets/f26cf4ed-638e-4e06-b512-6077a6b2085f)

#### GitHub에 커밋 시 자동으로 빌드 트리거가 작동하도록 설정한다.
![9999-1](https://github.com/user-attachments/assets/1cad4450-6ccc-4b40-994d-4e977542bf68)

## 🔍 교훈 및 적용
코드 변경사항이 실시간으로 반영되어 빠른 배포
- GitHub Webhook을 Jenkins와 연동하여 커밋 시 자동으로 빌드가 실행되도록 설정했다.

## 🌱 발전
Jenkins와 Ngrok을 활용하여 CI/CD 파이프라인을 구축하고 운영함으로써, 개발과 배포 간의 시간 차이를 크게 줄일 수 있었다.
