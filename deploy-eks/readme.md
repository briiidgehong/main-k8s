```
# 자주쓰는 명령어




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

















# 
![스크린샷 2023-02-22 오후 9 35 11](https://user-images.githubusercontent.com/73451727/220621374-d2b8b5c0-cc90-4824-be65-4b729201729e.png)
![스크린샷 2023-02-22 오후 9 35 16](https://user-images.githubusercontent.com/73451727/220621369-89bc7ef6-016f-461a-9ee5-37b378794c1c.png)
![스크린샷 2023-02-22 오후 9 35 21](https://user-images.githubusercontent.com/73451727/220621360-8e13d417-b1f4-4563-b598-1278271a989e.png)
![스크린샷 2023-02-22 오후 9 35 28](https://user-images.githubusercontent.com/73451727/220621353-63efb7eb-c681-4680-a963-71d6325a2a8b.png)




