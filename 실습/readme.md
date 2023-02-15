## 로컬 실습
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
