# jenkinsTest
0. 포트포워딩
<br>![9999-2](https://github.com/user-attachments/assets/0b9c8f4d-d351-49a8-a253-e1720d2b35dc)

## ngrok
```bash
# CMD
$ ngrok config add-authtoken ______
$ cd C:\02.devEnv
$ ngrok http http://localhost:8080
```
<!-- ngrok config add-authtoken 2mazkbTnosXgNxUn7GmF9DCltsR_c2eF9uiFGTPBtf3TxBqn -->

## Github
```bash
# Linux
$ ngrok http http://localhost:8888
```
Forwarding에 나온 url을 Payload URL에 입력하고 /github-webhook/ 추가
![5555](https://github.com/user-attachments/assets/f5b169b9-e956-4bc4-9d53-9b4ae238e89f)

## Jenkins
```bash
# Linux
$ docker run --name myjenkins --privileged -p 8888:8080 jenkins/jenkins:lts-jdk17

$ docker ps
# jenkins가 실행되지 않았을 경우 jenkins 컨테이너 이름 실행
$ docker start myjenkins
```
1. localhost:8888 접속 > 로그인
![1111](https://github.com/user-attachments/assets/56b9d046-4c72-4506-b5ee-ab88059ab3c3)
![6666](https://github.com/user-attachments/assets/78bdd879-5623-4bf7-90b4-f9a187cadfae)
<!-- 아이디 : admin, 비밀번호 : 59361ec075324f82aeb03d699a003e66 -->
![2222](https://github.com/user-attachments/assets/8d449ae9-14de-4c81-9955-21d2d4f23a39)

2. Dashboard > 새로운 Item > Ok
![3333](https://github.com/user-attachments/assets/5d923774-85da-4874-a047-efc0f2880add)

3. Dashboard > item name > Configuration > Pipeline
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

4. Dashboard > Jenkins 관리 > Plugins > Available plugins > stage view 설치
![7777](https://github.com/user-attachments/assets/75a3c0ca-8b5f-4da6-af92-dc41f58e2b95)
![8888](https://github.com/user-attachments/assets/91b44c06-bfe7-4efe-9012-7a94a0fe9219)

5. localhost:8888 > item name > 지금 빌드
```bash
# Linux
$ docker ps
# jenkins가 실행되지 않았을 경우 실행
$ docker start myjenkins
```
![9999-3](https://github.com/user-attachments/assets/f26cf4ed-638e-4e06-b512-6077a6b2085f)

6. 깃허브에 커밋 시 자동 빌드
![9999-1](https://github.com/user-attachments/assets/1cad4450-6ccc-4b40-994d-4e977542bf68)
