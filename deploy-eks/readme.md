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

