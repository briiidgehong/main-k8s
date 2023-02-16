## 로컬 실습

## kubectl / minikube 설치
<img width="984" alt="스크린샷 2023-02-15 오후 6 49 11" src="https://user-images.githubusercontent.com/73451727/218992970-15c703e2-3305-495a-ad9c-54f769233ba3.png">

```
# 클러스터와의 통신을 위해 kubectl 설치
brew install kubectl
kubectl version --client

# 미니큐브 (로컬 쿠버네티스 클러스터) 설치
brew install minikube
minikube start --driver=docker

docker ps

minikube status
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

minikube dashboard

```

## 쿠버네티스 객체
<img width="868" alt="스크린샷 2023-02-16 오후 7 15 10" src="https://user-images.githubusercontent.com/73451727/219336487-473cb00f-e927-41d7-88ad-ed5e7be4c788.png">
<img width="855" alt="스크린샷 2023-02-16 오후 7 29 22" src="https://user-images.githubusercontent.com/73451727/219339965-1cd265f3-c207-4ed5-b991-3e17ff9dcd91.png">
<img width="840" alt="스크린샷 2023-02-16 오후 7 36 37" src="https://user-images.githubusercontent.com/73451727/219341716-59f55aae-5f74-4c35-9ff7-fc16bd3b2d0d.png">


```
쿠버네티스는 객체와 함께 동작한다.
쿠버네티스 객체를 두가지 방식으로 생성할수 있음
1. 명령
2. 선언

# Pods objects
- 쿠버네티스와 상호작용하는 가장 작은 단위
- 일반적으로 하나의 팟에 하나의 컨테이너를 가짐 (더 많은 컨테이너를 가질수 있다.)
- 모든 팟이 공유하는 볼륨을 가질수 있다.
- 로컬호스트를 사용하여 팟 내부의 컨테이너들끼리 커뮤니케이트 할수있다.
- 팟은 기본적으로 임시적이다.(볼륨과같은것으로 남길수는 있으나 데이터가 날아가는게 기본)
- 컨트롤러에 의해(= deployment 객체에 의해) 자동 배포가 관리된다. 

# deployment objects
- 팟을 컨트롤하고 관리한다.
- 원하는 설정을 하면 쿠버네티스가 설정과 동일하게 만들어준다. (팟의 갯수/컨테이너의 수 ''')
- 배포는 중지/삭제/롤백될수 있다.
- 설정된만큼 유동적으로 스케일이 가능하다.
- 여러개의 배포 설정을 생성할수 있다.
- 팟을 직접 개발자가 생성하고 관리하지 않는다. deployment 객체에 원하는 설정과 상태를 정의할뿐

```

## 실습 1 쿠버네티스에 node js app 올리기
<img width="838" alt="스크린샷 2023-02-16 오후 11 36 12" src="https://user-images.githubusercontent.com/73451727/219394405-f882067f-6afb-446b-9b0b-06a91f71f22c.png">
<img width="843" alt="스크린샷 2023-02-16 오후 11 46 04" src="https://user-images.githubusercontent.com/73451727/219396935-68e6aabf-ca6f-4b62-a6f4-87c41c80c0a2.png">

```
# 이미지 파일 만들기
docker build -t kub-first-app .

minikube status
minikube start --driver=docker

# kubectl이 바라보고 있는 클러스터 확인 ??? - 미니큐브이어야함

# 새로운 객체 만들기
kubectl create help

# dockerhub에 이미지 올리기
- dockerhub에서 리포지토리 생성: bridgehong/k8s-test-app
- docker login (dockerhub 계정: bridgehong )
- 기존 image에 리포지토리 tag 붙이기
docker tag kub-first-app bridgehong/k8s-test-app
docker images (image명 변경된것 확인)
docker push bridgehong/k8s-test-app

# --image: first-deployment에서 생성된 pod의 컨테이너에 사용할 이미지를 지정
# 쿠버네티스 클러스터에 있는 마스터 노드(=컨트롤플레인) 으로 전송
# 컨트롤 플레인은 워커 노드에 pod를 배포하는 일을 담당한다.
# 현재 실행중인 pod를 분석하여 새로 생성된 pod에 가장 적합한 워커 노드를 찾는다.
# 새로 생성된 이 pod은 워커 노드중 하나로 보내집니다.

kubectl create deployment first-deployment --image=bridgehong/k8s-test-app
deployment.apps/first-deployment created

kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
first-deployment   1/1     1            1           18s
hello-minikube     1/1     1            1           16d

kubectl get pods
NAME                                READY   STATUS    RESTARTS     AGE
first-deployment-5d979c85f9-nhd22   1/1     Running   0            37s
hello-minikube-7ddcbc9b8b-qxhjq     1/1     Running   3 (9h ago)   16d

minikube dashboard - pods 올라간것 보기

# 서비스 객체
# pod와 pod에서 실행되는 컨테이너에 접근하여면 service가 필요합니다.
# pod을 클러스터와 외부에 노출시켜준다.
# pod은 내부 ip 주소를 가지지만 너무 자주 바뀐다.(스케일링 / 리플레이스)
# 서비스는 pod을 그룹화하고, 공유 IP 주소를 제공한다. (이 주소는 바뀌지 않는다.)
# 이 공유 IP는 외부에서도 접근 가능하다. (= 즉 외부에서 pod에 접근 가능해진다.)

# 서비스 생성
# 서비스를 생성하여, deployment에 의해 생성된 pod를 노출합니다.
# --type=ClusterIP / default, 기본적으로 클러스터 내부에서만 접근 가능
# --type=NodePort / deployment가 실행 되고있는 워커 노드의 IP 주소를 통해 포트가 노출된다.
# --type=LoadBalancer / 클러스터가 실행되는 인프라에 존재하는 로드밸런서를 활용
# 클러스터가 실행되는 인프라(프로바이더) 가 로드밸런서를 지원해야 사용 가능
# 로드밸런서는 이 서비스에 대한 고유 주소를 생성하고,
# 들어오는 트래픽을 이 서비스의 일부인 모든 pod에 고르게 분산
kubectl expose deployment first-deployment --type=LoadBalancer --port=8080

# 서비스를 생성하고 프로바이더에 배포되면 external-ip 조회 가능
kubectl get services

# 미니큐브 한정 external-ip 조회 (로컬용임 / 프로바이더 배포시에는 위의 명령어로 조회 가능)
minikube service first-deployment
|-----------|------------------|-------------|---------------------------|
| NAMESPACE |       NAME       | TARGET PORT |            URL            |
|-----------|------------------|-------------|---------------------------|
| default   | first-deployment |        8080 | http://192.168.49.2:30902 |
|-----------|------------------|-------------|---------------------------|
🏃  first-deployment 서비스의 터널을 시작하는 중
|-----------|------------------|-------------|------------------------|
| NAMESPACE |       NAME       | TARGET PORT |          URL           |
|-----------|------------------|-------------|------------------------|
| default   | first-deployment |             | http://127.0.0.1:56177 |
|-----------|------------------|-------------|------------------------|



```
