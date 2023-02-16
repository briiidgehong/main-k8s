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
```
# 이미지 파일 만들기
docker build -t kub-first-app .

minikube status
minikube start --driver=docker

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

```
