## DEPLOY OPTION
![스크린샷 2023-02-22 오후 9 35 05](https://user-images.githubusercontent.com/73451727/220621378-c039449c-76eb-4cad-8594-ced5ac923bed.png)
```

클라우드 프로바이더를 이용한다면, 다시 두 가지 선택을 해야 합니다.

1. 자체적으로 EC2 올려서, 필요한 쿠버네티스 소프트웨어를 설치하고 네트워크 등을 설정
   kops와 같은 툴을 통해 필요한 설치에 도움을 받을 수 있으나 이 역시 까다로움

2. 관리형 서비스를 이용하는 경우,
   일반적인 클러스터 아키텍처만 정의하면, 관리형 서비스가 필요한 모든 머신을 구동하고
   필요한 소프트웨어를 모두 설치하여, 필요한 네트워크를 설정한다.
   
```

# ECS VS EKS
![스크린샷 2023-02-22 오후 9 35 11](https://user-images.githubusercontent.com/73451727/220621374-d2b8b5c0-cc90-4824-be65-4b729201729e.png)
```
AWS EKS: Elastic Kubernetes Service
AWS ECS: Elastic Container Service

둘의 차이점은 EKS는 쿠버네티스 배포를 위한 서비스

AWS EKS를 사용하면, 쿠버네티스의 구문, 철학 및 접근 방식을 사용할 수 있으며, AWS 특정 구문이나, 설정이 필요하지 않습니다.
쿠버네티스에 대해 학습한대로, 정적 시스템 구성을 사용할 수 있습니다.

```

# 
![스크린샷 2023-02-22 오후 9 35 16](https://user-images.githubusercontent.com/73451727/220621369-89bc7ef6-016f-461a-9ee5-37b378794c1c.png)
![스크린샷 2023-02-22 오후 9 35 21](https://user-images.githubusercontent.com/73451727/220621360-8e13d417-b1f4-4563-b598-1278271a989e.png)
![스크린샷 2023-02-22 오후 9 35 28](https://user-images.githubusercontent.com/73451727/220621353-63efb7eb-c681-4680-a963-71d6325a2a8b.png)

## VPC - SUBNET SETTING
### 1. CIDR: 192.168.0.0/16 - 네트워크 비트가 16개 - network: 192.168 host 0.0 - 2의 16제곱 = 약 65536 개의 호스트 IP 설정 가능
<img width="578" alt="스크린샷 2023-02-24 오후 6 46 31" src="https://user-images.githubusercontent.com/73451727/221146740-665d092f-4a99-4f96-b675-2d782f52e08c.png">
<img width="674" alt="스크린샷 2023-02-24 오후 6 37 54" src="https://user-images.githubusercontent.com/73451727/221145905-7911a7b9-621d-4d64-b6b5-008785c559ff.png">

### 2. SUBNET
#### PUBLIC SUBNET: 192.168.10.0/24 - 네트워크 비트가 24개 - host = 8bit - 2의 8제곱 약 256개의 호스트 IP 설정 가능
#### PRIVATE SUBNET: 192.168.11.0/24 - 네트워크 비트가 24개 - host = 8bit - 2의 8제곱 약 256개의 호스트 IP 설정 가능
<img width="656" alt="스크린샷 2023-02-24 오후 6 51 51" src="https://user-images.githubusercontent.com/73451727/221148538-957dbdfd-11f7-48c3-979b-1098401ae388.png">
<img width="656" alt="스크린샷 2023-02-24 오후 6 52 40" src="https://user-images.githubusercontent.com/73451727/221148566-ba263b32-aebd-4ae4-a070-b2ecfaf497b9.png">

## 3. ROUTING TABLE
### kube-vpc-public-subnet-route
### 1) 라우팅테이블 생성
### 2) 명시적 서브넷 연결 (kube-vpc-public-subnet)
### 3) IGW 생성 / IGW-VPC 연결
### 4) 라우팅 편집 - 추가 0.0.0.0/0 IGW
<img width="669" alt="스크린샷 2023-02-24 오후 7 01 47" src="https://user-images.githubusercontent.com/73451727/221150394-55e0dc2e-39d5-4303-90af-c37851593f3b.png">

<img width="669" alt="스크린샷 2023-02-24 오후 7 07 03" src="https://user-images.githubusercontent.com/73451727/221151986-a12078a4-ebdf-4d61-83a5-48740be52a5b.png">
<img width="661" alt="스크린샷 2023-02-24 오후 7 08 45" src="https://user-images.githubusercontent.com/73451727/221152010-7fe80fbc-7ad5-4e26-9a81-e0b3329d2913.png">
##
<img width="1751" alt="스크린샷 2023-02-24 오후 7 10 47" src="https://user-images.githubusercontent.com/73451727/221152192-ee270c5a-80db-40ea-929f-83fa3954556f.png">

### kube-vpc-private-subnet-route
### 라우팅테이블 생성
### 서브넷 연결 ecs-test-private-subnet-01
<img width="663" alt="스크린샷 2023-02-24 오후 7 02 13" src="https://user-images.githubusercontent.com/73451727/221150411-57eca9ef-2abc-46a7-9e59-012f19a2ea83.png">



