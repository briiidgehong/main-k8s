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
kubectl config
kubectl config set-cluster 
Set a cluster entry in kubeconfig.

 Specifying a name that already exists will merge new fields on top of existing values for those fields.

Examples:
  # Set only the server field on the e2e cluster entry without touching other values
  kubectl config set-cluster e2e --server=https://1.2.3.4

  # Embed certificate authority data for the e2e cluster entry
  kubectl config set-cluster e2e --embed-certs --certificate-authority=~/.kube/e2e/kubernetes.ca.crt

  # Disable cert checking for the e2e cluster entry
  kubectl config set-cluster e2e --insecure-skip-tls-verify=true

  # Set custom TLS server name to use for validation for the e2e cluster entry
  kubectl config set-cluster e2e --tls-server-name=my-cluster-name

  # Set proxy url for the e2e cluster entry
  kubectl config set-cluster e2e --proxy-url=https://1.2.3.4


kubectl cluster-info

kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority: /Users/jyhong/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Fri, 17 Feb 2023 00:09:14 KST
        provider: minikube.sigs.k8s.io
        version: v1.29.0
      name: cluster_info
    server: https://127.0.0.1:56138
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Fri, 17 Feb 2023 00:09:14 KST
        provider: minikube.sigs.k8s.io
        version: v1.29.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /Users/jyhong/.minikube/profiles/minikube/client.crt
    client-key: /Users/jyhong/.minikube/profiles/minikube/client.key


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

# CONTAINER CRUSH TEST
```
minikube service first-deployment
 kubectl get pods
NAME                                READY   STATUS    RESTARTS      AGE
first-deployment-5d979c85f9-nhd22   1/1     Running   1 (17h ago)   21h
hello-minikube-7ddcbc9b8b-qxhjq     1/1     Running   4 (17h ago)   16d

# CRUSH CONTAINER
http://127.0.0.1:63591/error
app.get('/error', (req, res) => {
  process.exit(1);
});

 kubectl get pods
NAME                                READY   STATUS    RESTARTS      AGE
first-deployment-5d979c85f9-nhd22   0/1     Error     2 (58s ago)   21h
hello-minikube-7ddcbc9b8b-qxhjq     1/1     Running   4 (17h ago)   16d

# deployment에 정의된대로 다시 컨테이너가 재시작됨
 kubectl get pods
NAME                                READY   STATUS    RESTARTS      AGE
first-deployment-5d979c85f9-nhd22   1/1     Running   3 (16s ago)   21h
hello-minikube-7ddcbc9b8b-qxhjq     1/1     Running   4 (17h ago)   16d

# pods을 스케일 아웃 하기
kubectl scale deployment/first-app --replicas=3
 kubectl get pods
NAME                                READY   STATUS              RESTARTS        AGE
first-deployment-5d979c85f9-85f5z   0/1     ContainerCreating   0               3s
first-deployment-5d979c85f9-b8l6v   0/1     ContainerCreating   0               3s
first-deployment-5d979c85f9-nhd22   1/1     Running             3 (4m14s ago)   21h
hello-minikube-7ddcbc9b8b-qxhjq     1/1     Running             4 (17h ago)     16d

# error/ 해도, 다시 새로고침하면 페이지에 연결 잘됨 / 다른 pod으로 골고루 로드밸런서가 트래픽 분산처리중이기 때문에

```

# 롤백 활용
<img width="1026" alt="스크린샷 2023-02-17 오후 5 58 15" src="https://user-images.githubusercontent.com/73451727/219599467-f81e41e5-003e-42d1-a7f2-1038e5eb1a94.png">

```
# 코드 수정

# image build / dockerhub push / tag: 2
docker build -t bridgehong/k8s-test-app:2 .
docker images
docker push
docker push bridgehong/k8s-test-app:2

# deployment image update (k8s-test-app: 컨테이너 이름)
# 다른 태그가 감지되면 쿠버네티스는 이미지를 새로 다운로드하고, 컨테이너를 재실행 시킨다.
kubectl set image deployment/first-deployment k8s-test-app=bridgehong/k8s-test-app:2 
deployment.apps/first-deployment image updated

# kubectl rollout status 현재 deployment에 의해 어떤것들이 진행되고 있는지 -f 하게 실시간으로 출력해줌
kubectl rollout status deployment/first-deployment 
deployment "first-deployment" successfully rolled out


# 가장 최근의 deployment 내리기
kubectl rollout undo deployment/first-deployment

# deploy revision 조회
kubectl rollout history deployment/first-deployment
deployment.apps/first-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

# 좀 더 상세하게 이미지와 태그 확인 가능
kubectl rollout history deployment/first-deployment --revision=2

# revision 1 으로 rollback
kubectl rollout undo deployment/first-deployment --to-revision=1

# 테스트용 리소스 제거
kubectl delete service first-deployment
kubectl delete deployment first-deployment


```

# 명령적 접근방식 -> 선언적 접근방식
<img width="861" alt="스크린샷 2023-02-17 오후 6 10 41" src="https://user-images.githubusercontent.com/73451727/219602453-eef2fdce-375a-48ef-9816-41c20b02734a.png">

```
"docker run을 계속 쳐야할때 -> docker-compose file로 정의되면 편하다"
쿠버네티스에도 위와 비슷하게 클러스터 구성/배포 파일이 있음

deployment.yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata: 
    name: second-app-deployment
  spec: 
    replicas: 3
    selector:
      matchLabels:
        app: second-app
        tier: backend
    template: 
      metadata: 
        labels:
          app: second-app
          tier: backend
      spec:
        containers:
          - name: second-app-container
            image: bridgehong/bridgehong/k8s-test-app:2
          # - name: ...
          #   image: ...

kubectl apply -f=./deployment.yaml 
deployment.apps/second-app-deployment created

kubectl get deployments
kubectl get pods

kubectl apply -f=./service.yaml   
service/backend created

kubectl get services 
NAME             TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
backend          LoadBalancer   10.109.54.3      <pending>     80:30635/TCP     22s
hello-minikube   NodePort       10.104.186.179   <none>        8080:31078/TCP   17d
kubernetes       ClusterIP      10.96.0.1        <none>        443/TCP          17d

minikube service backend 
|-----------|---------|-------------|---------------------------|
| NAMESPACE |  NAME   | TARGET PORT |            URL            |
|-----------|---------|-------------|---------------------------|
| default   | backend |          80 | http://192.168.49.2:30635 |
|-----------|---------|-------------|---------------------------|
🏃  backend 서비스의 터널을 시작하는 중
|-----------|---------|-------------|------------------------|
| NAMESPACE |  NAME   | TARGET PORT |          URL           |
|-----------|---------|-------------|------------------------|
| default   | backend |             | http://127.0.0.1:64119 |
|-----------|---------|-------------|------------------------|

# 파일 변경 후 재적용
kubectl apply -f=./deployment.yaml  
kubectl apply -f=./service.yaml  

# 해당 파일에 의해 만들어진 리소스 삭제
kubectl delete -f=deployment.yaml -f=service.yaml

# 하나의 파일로 사용 -> master-deployment.yaml
# --- 로 객체를 구분
# 순서는 위에서부터 아래순으로 실행됨

```

## LABEL / SELECTOR
```
kind: Service
spec:
  # 서비스의 대상이 되는 pod 리소스를 가르킴
  # selector 작동방식이 왜 deployment 객체의 selector와 다를까?
  # apiversion이 다르기때문(=matchLabels는 더 최신버전에서만 지원함), 각 객체가 사용하는 버전이 다르다.
  selector:
    # tier가 frnotend이건 backend이건, app: second-app 레이블을 가진 모든 리소스에 연결됨
    # 이 서비스 객체가 app:second-app 레이블이 있는 모든 pod을 연결해서 노출시킴
    app: second-app
    # tier: frontend? backend?

kind: Deployment
# 생성하는 객체의 이름을 정의
metadata:
  name: second-app-deployment
# deployment 객체의 사양을 정의(=deployment 객체이므로, pod에 대한 정의)
spec:
  replicas: 2
  # 이 deployment와 일치시키려는 pod 레이블의 키-벨류 를 지정 !!!
  # 즉, template가 여러개일수 있고 이 deployment에 어떤 pod을 올릴건지 선택할 수 있음 !!!
  selector:
    matchLabels:
      # tier: 계층 (보통 frontend / backend)
      app: second-app
      tier: backend
      
    # 보다 더 현대적인 selector option
    # matchLabels와 달리, 레이블에 키 값 쌍이 여러 개 중첩되지 않으며,
    # 대신 일치하는 객체를 갖기 위해 모두 충족되어야 하는 표현식의 목록을 가집니다.
    # operator를 통해 더욱 유현하게 셀렉팅이 가능함
    # In: values에 속하는 리소스를 선택 / NotIn: values에 속하는 리소스를 제외한 나머지 리소스를 선택 
    # matchExpressions
    #   - {key: app, operator: In, values: [second-app, third-app]}

  # template: deployment객체의 일부로 생성해야 하는 pod을 정의
  # kind가 필요없음 - template 자체가 pod을 정의하는 필드이기 때문에
  template:
    # pod은 쿠버네티스의 새로운 객체이므로, metadata 추가
    metadata:
      # 원하는 키-벨류 지정할 수 있다. (식별자 역할)
      # 주어진 객체에 하나 이상의 레이블을 선언할수 있고,
      # selector에서 키-쌍을 동일하게 입력해 해당 리소스를 연결한다.
      labels:
        app: second-app
        tier: backend
    # pod의 사양 정의
    # deployment당 한 가지 유형의 pod이 존재
    spec:
      # 컨테이너 추가, 한 pod에 여러개의 컨테이너를 띄울 수 있음
      containers:
        - name: nodejs-app-container
          image: academind/kub-first-app:2
        # - name: ...
        #   image: ...

```

## Liveness Probes
```
쿠버네티스가 pod와 컨테이너가 정상인지 아닌지의 여부를 확인하는 방법 like health check

spec:
  # 컨테이너 추가, 한 pod에 여러개의 컨테이너를 띄울 수 있음
  containers:
    - name: nodejs-app-container
      image:
        academind/kub-first-app:2
        # like health check
      livenessProbe:
        httpGet:
          path: /
          port: 8080
        # 10s
        periodSeconds: 10
        initialDelaySeconds: 5
    # - name: ...
    #   image: ...

```








































