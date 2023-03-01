```
# 자주쓰는 명령어


- kubectl endpoint 변경
- local minikube -> aws kube-cluster
- 설정파일 위치: /Users/jyhong/.kube/config 
- AWS CLI 이용해서 aws kube-cluster 로 엔드포인트 변경
- cat ~/.aws/credentials
- aws configure -> login / region 설정
aws eks --region ap-northeast-1 update-kubeconfig --name kube-cluster
Added new context arn:aws:eks:ap-northeast-1:744721539376:cluster/kube-cluster to /Users/jyhong/.kube/config

kubectl get pods
+ vscode extension에서도 클러스터 엔드포인트 스위칭 편하게 가능
+ eks version과 kubectl version 호환이 안될경우 kubectl downgrade 필요함

# helm chart
brew install helm
helm repo list


```

## 1. VPC - SUBNET SETTING
> ### 1. VPC 
> * CIDR: 192.168.0.0/16 - 네트워크 비트가 앞에서부터 16개 
> * network: 192.168 host 0.0 - 2의 16제곱 = 약 65536 개의 호스트 IP 설정 가능

> ### 2. SUBNET
> * 네트워크 비트가 24개 - host = 8bit - 2의 8제곱 약 256개의 호스트 IP 설정 가능
> * kube-vpc-az1-dev-private-subnet: 192.168.10.0/24
> * kube-vpc-az1-dev-public-subnet: 192.168.11.0/24
> <br/>

> * kube-vpc-az1-prod-private-subnet: 192.168.20.0/24
> * kube-vpc-az1-prod-public-subnet: 192.168.21.0/24
> * kube-vpc-az2-prod-private-subnet: 192.168.22.0/24
> * kube-vpc-az2-prod-public-subnet: 192.168.23.0/24

> ### 3. ROUTING TABLE
> * ##### 1) IGW 생성: kube-IGW - 위에 생성해놓은 VPC에 연결
> * ##### 2) NGW 생성 
>    * az1-kube-NGW - !!!public subnet 연결!!! - kube-vpc-az1-prod-public-subnet
>    * az2-kube-NGW - !!!public subnet 연결!!! - kube-vpc-az2-prod-public-subnet
>    * NGW란, private subnet 외부에서 내부로의 접근은 차단, 내부에서 외부의 접근은 허용
>    * 예를 들어 써드파티 패키지들을 다운로드하는 것이 가능해진다.
> * ##### 3) ROUTING TABLE 생성
>    * kube-vpc-public-subnet-route 생성
>       * 서브넷연결
>          * kube-vpc-az1-dev-public-subnet 
>          * kube-vpc-az1-prod-public-subnet 
>          * kube-vpc-az2-prod-public-subnet
>       * 라우팅추가: 0.0.0.0/0 - IGW
>    * kube-vpc-az1-private-subnet-route
>       * 서브넷연결
>         * kube-vpc-az1-prod-private-subnet
>          * kube-vpc-az1-dev-private-subnet
>       * 라우팅추가: 0.0.0.0/0 - az1-kube-NGW
>    * kube-vpc-az2-private-subnet-route
>       * 서브넷연결
>          * kube-vpc-az2-prod-private-subnet
>       * 라우팅추가: 0.0.0.0/0 - az2-kube-NGW  

> <img width="649" alt="스크린샷 2023-02-26 오전 11 26 39" src="https://user-images.githubusercontent.com/73451727/221511500-45055b96-0134-4b59-b1d7-435295984e67.png">
> <img width="1472" alt="스크린샷 2023-02-27 오후 5 31 25" src="https://user-images.githubusercontent.com/73451727/221519187-f07b6fd3-555c-470a-aa13-81ef5840ddf4.png">
> <img width="1158" alt="스크린샷 2023-02-27 오후 6 00 13" src="https://user-images.githubusercontent.com/73451727/221519207-9430bc1c-d65d-47d5-a4ae-99ba7cca9a25.png">

## 2. EKS SETTING
> #### EKS를 쓰는 이유 (= 관리형 서비스를 사용하는 이유)
> ![스크린샷 2023-02-22 오후 9 35 05](https://user-images.githubusercontent.com/73451727/220621378-c039449c-76eb-4cad-8594-ced5ac923bed.png)
<br/>

> ##### 클라우드 프로바이더를 이용한다면, 두가지 선택지가 존재함
> ##### 1. 자체적으로 EC2 올려서, 필요한 쿠버네티스 소프트웨어를 설치하고 네트워크 등을 설정
> ##### 2. 관리형 서비스를 이용하는 경우
> ##### 일반적인 클러스터 아키텍쳐만 정의하면, 관리형 서비스가 필요한 모든 머신을 구동하고
> ##### 필요한 소프트웨어를 모두 설치하며 필요한 네트워크 설정을 한다.
> ##### 자체 설치에 필요한 패키지가 존재하긴 하지만 이역시 까다로워서 보통은 관리형 서비스를 이용한다.
<br/>

> ### 1) 클러스터 구성
> <img width="555" alt="스크린샷 2023-02-27 오후 6 24 22" src="https://user-images.githubusercontent.com/73451727/221524828-c2e4fb98-2bc5-4222-91a8-2c6fccad8d3c.png">

> ##### EKSClusterRole 생성 -> 클러스터 서비스 역할에서 선택하여 사용
> ##### IAM -> 역할 -> 역할만들기 -> AWS 서비스 -> EKS -> EKS-cluster 선택 / 생성
<br/>

> ### 2) 네트워크 구성
> * 서브넷 구성 (최소 두개의 AZ)
>   * kube-vpc-az1-prod-private-subnet
>   * kube-vpc-az1-prod-public-subnet
>   * kube-vpc-az2-prod-private-subnet
>   * kube-vpc-az2-prod-public-subnet
> * 클러스터 엔드포인트 엑세스 - 퍼블릭 및 프라이빗
>   * 외부에서 접근할 수 있는 특정 엔트포인트를 생성
>   * 노드to노드 / pod to pod 간의 트레픽은 클러스터 내부에서 돌림
> <img width="551" alt="스크린샷 2023-02-27 오후 6 32 25" src="https://user-images.githubusercontent.com/73451727/221526881-eb253785-3b74-402d-aae1-e49da56763f0.png">
> <img width="552" alt="스크린샷 2023-02-27 오후 6 33 12" src="https://user-images.githubusercontent.com/73451727/221526895-1e663162-a990-487b-b9f2-e3869838cacb.png">
<br/>

> ### 3) 로깅 구성
> <img width="349" alt="스크린샷 2023-02-27 오후 6 40 19" src="https://user-images.githubusercontent.com/73451727/221528461-c3a9617f-9f6c-45ca-9400-963dffef1ad3.png">
<br/>

> ### 4) 추가기능 구성
> <img width="579" alt="스크린샷 2023-02-27 오후 6 41 53" src="https://user-images.githubusercontent.com/73451727/221528917-b964e4ea-790d-47a1-aad3-920e789cf5c0.png">
<br/>

> ### 5) 검토 및 생성

## 3. EKS 에 노드 추가
> ##### (=쿠버네티스 소프트웨어가 설치된 리모트 머신 추가)
> * 1) 워커노드 추가
>   * 클러스터 -> 컴퓨텅 -> 노드그룹 추가
>     * IAM role 생성 및 mapping - EKSNodeGroup
>       * IAM -> aws service -> ec2
>         * AmazonEKSWorkerNodePolicy 추가
>         * AmazonEKS_CNI_Policy 추가
>         * AmazonEC2ContainerRegistryReadOnly 추가
> * computing 유형 t3.small 이상 선택
> * 선택하려는 subnet 설정에 'enable auto-assign public IPv4 address' 활성화 되어있어야함
> * az1-prod-public-subnet / az2-prod-public-subnet
> * EC2 생성 확인 !
> <img width="642" alt="스크린샷 2023-02-27 오후 8 24 02" src="https://user-images.githubusercontent.com/73451727/221551662-cd4a0e4c-1fe8-408b-84fc-65bd19f72b64.png">
> <img width="586" alt="스크린샷 2023-02-27 오후 8 22 24" src="https://user-images.githubusercontent.com/73451727/221551685-40a5778f-ff6a-4daa-b8ac-a76a741b410f.png">
> <img width="646" alt="스크린샷 2023-02-27 오후 8 27 43" src="https://user-images.githubusercontent.com/73451727/221552357-bcd3e1d9-0e71-4488-bb0d-9539a97fa69f.png">
> <img width="644" alt="스크린샷 2023-02-27 오후 8 28 02" src="https://user-images.githubusercontent.com/73451727/221552374-905f6a37-aee6-46c1-8dec-0ac67d26a033.png">
<img width="659" alt="스크린샷 2023-03-01 오전 11 11 33" src="https://user-images.githubusercontent.com/73451727/222027658-2d92e211-f681-4902-bdcc-229faa3eaf89.png">
<img width="657" alt="스크린샷 2023-03-01 오전 11 13 07" src="https://user-images.githubusercontent.com/73451727/222027680-0344e40e-06f3-4172-97c2-97d4d663488d.png">
<img width="1254" alt="스크린샷 2023-03-01 오전 11 16 17" src="https://user-images.githubusercontent.com/73451727/222027973-9c93a479-414a-4953-aa0d-006b3697cd3e.png">






> ##### 1) kubectl endpoint aws eks-cluster로 변경
> ```
> aws eks --region ap-northeast-1 update-kubeconfig --name kube-cluster
> kubectl apply -f=auth.yaml -f=users.yaml
> get deployments
> kubectl get pods
> kubectl get services


> ```
> 












# 
![스크린샷 2023-02-22 오후 9 35 11](https://user-images.githubusercontent.com/73451727/220621374-d2b8b5c0-cc90-4824-be65-4b729201729e.png)
![스크린샷 2023-02-22 오후 9 35 16](https://user-images.githubusercontent.com/73451727/220621369-89bc7ef6-016f-461a-9ee5-37b378794c1c.png)
![스크린샷 2023-02-22 오후 9 35 21](https://user-images.githubusercontent.com/73451727/220621360-8e13d417-b1f4-4563-b598-1278271a989e.png)
![스크린샷 2023-02-22 오후 9 35 28](https://user-images.githubusercontent.com/73451727/220621353-63efb7eb-c681-4680-a963-71d6325a2a8b.png)




