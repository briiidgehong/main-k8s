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

## 쿠버네티스 볼륨
<img width="844" alt="스크린샷 2023-02-20 오후 6 13 56" src="https://user-images.githubusercontent.com/73451727/220065100-fbc92bb2-dcc4-4b3e-862b-00b2812ecf8b.png">
<img width="865" alt="스크린샷 2023-02-20 오후 6 19 32" src="https://user-images.githubusercontent.com/73451727/220065130-b2d75ac2-6f6f-4e5c-aa4c-ca8b23581149.png">
<img width="836" alt="스크린샷 2023-02-20 오후 6 23 02" src="https://user-images.githubusercontent.com/73451727/220065244-0cb21433-239c-4744-8041-eaddd63db09f.png">


```
컨테이너가 종료되거나, 컨테이너를 호스팅하는 pod가 제거되거나, 
혹은 확장되거나, 혹은 노드 간에 이동되는 경우,
컨테이너에서 생성된 모든 데이터가 유지되도록 보장할 수 있는 방법

볼륨의 종류: volume(일반볼륨) / persistent volume(영구볼륨) / persistent volume claims(영구볼륨 클레임)

볼륨은 쿠버네티스에 의해 시작되고 관리되는 pod의 일부이다. 
-> pod의 라이프타임과 함께한다. -> 컨테이너를 삭제하고 재시작해도 살아있다. -> pod이 제거되면 볼륨도 함께 제거된다.

로컬머신의 디렉토리를 이용하는 도커 컴포즈와 달리 다양한 타입의 볼륨을 지원
- cloud provider volume: aws-elastic-block-store

pod을 정의할때에 볼륨도 같이 정의 - pod의 모든 컨테이너에서 해당 볼륨을 사용 가능하다.

# 볼륨 유형1. emptyDir -> pod이 하나일때 적합함
# emptyDir type: pod이 시작될 떄마다 새로운 빈 디렉토리를 생성
# 컨테이너가 재시작되거나 삭제되더라도 데이터는 살아있음 - pod이 제거되는 경우, 해당 디렉토리는 삭제된다.
# volumeMount를 이용해, 볼륨과 컨테이너를 연결
volumes:
        - name: story-volume
          # emptyDir: {}

# 볼륨 유형2.hostPath -> 노드가 하나일때 적합함
# emptyDir은 2개의 레플리카가 실행되는 경우 pod 각각 따로따로 데이터를 저장함
# 호스트머신 상의 디렉토리에 데이터를 저장 - pod안의 컨테이너와 마운트
# 여러개의 pod가 호스트 머신에서 하나의 동일한 경로를 공유하는 형태

# 호스트머신 상의 디렉토리를 저장소로 사용함
# hostPath:
#   호스트머신 상의 디렉토리 경로
#   path: /data
#   저장소가 없으면, 생성
#   type: DirectoryOrCreate

# 볼륨 유형3. CSI(container Storage Interface)
# 매우 유연한 볼륨의 형태
# 대신 명확하게 정의된 인터페이스를 노출합니다.
# 그것으로 누구나 이 인터페이스를 활용하는 드라이버 솔루션을 구축할 수 있습니다.

# CSI 기능 덕분에 EFS를 쿠버네티스 볼륨의 스토리지 솔루션으로 추가하는 것이 매우 쉬워집니다.
# 즉, CSI 볼륨 유형은 매우 유연한 유형이며,
# 이 AWS EFS(Amazon Elastic File System) CSI 드라이버와 같은,
# 이 CSI 유형에 대한 통합과 이러한 공식 드라이버가 많이 존재하는 한,
# 전 세계의 모든 스토리지 솔루션을 연결할 수 있습니다.
```

<img width="850" alt="스크린샷 2023-02-20 오후 7 36 21" src="https://user-images.githubusercontent.com/73451727/220081896-1dcf39d9-edd4-4a0c-a9d9-cc7a8a101963.png">
<img width="840" alt="스크린샷 2023-02-20 오후 7 43 26" src="https://user-images.githubusercontent.com/73451727/220083347-5ad8c264-03c6-4733-80fe-c2c48e9ce058.png">

```
# 영구 볼륨 -> "영구 볼륨은 Pod 및 노드 독립성에 대한 아이디어를 기반으로 구축됩니다." !!!

영구 볼륨은 Pod와 Node에 독립적입니다.
AWSElasticBlockStore, AzureDisc, AzureFile, NFS 결국에는 Pod 및 노드에 독립적인 스토리지를 제공합니다.
노드 외부에 데이터를 저장합니다.
awsElasticBlockStore를 사용한다면, 데이터가 AWS 서버에 저장되고, 그곳에서 제거되지 않습니다.

영구 볼륨 개념은 단순한 독립 스토리지, 그 이상입니다.
그 핵심 아이디어는 볼륨이 Pod에서 분리된다는 겁니다.
그리고 여기에는 Pod 수명 주기와 완전히 분리되는 것이 포함됩니다.
영구 볼륨으로 해당 Pod 및 노드에 대한 독립성을 갖게 되며,
클러스터 관리자로서 이 볼륨이 구성되는 방식에 대한 완전한 권한을 갖게 됩니다.

각각의 Pod와 각각의 deployment.yaml 파일, 또는 이와 유사한 파일에 대해 여러 번 구성할 필요가 없습니다.
대신 한 번만 정의한 다음, 원하는 경우 여러 Pod에서 그를 사용할 수 있습니다.
즉, "영구 볼륨은 Pod 및 노드 독립성에 대한 아이디어를 기반으로 구축됩니다." !!!

여러 노드와 여러 Pod가 있는 클러스터가 있는 경우,
동일한 Pod의 다른 인스턴스 또는
이러한 노드에서 실행 중인 다른 컨테이너를 지닌 여러 Pod가 존재합니다.
여러분은 이러한 Pod 내부에 볼륨이 있음을 배우게 됩니다.

영구 볼륨을 사용하면,
클러스터에 새 리소스, 새 엔터티를 가지게 되며,
그것은 노드 및 Pod에서 분리된다는 것이 아이디어 입니다.

그 대신, 영구 볼륨 클레임이라는 것을 만들 수 있습니다.
Pod 및 Pod가 실행되는 노드에 속하며 영구 볼륨과 연결하여 액세스를 요청할 수 있습니다.

여러 개의 개별적인 영구 볼륨에 대한 클레임을 가질 수 있습니다.
그리고 다양한 노드의 서로 다른 Pod에서 서로 다른 볼륨에 대해 서로 다른 클레임을 가질 수 있으며,
이로인해 완전한 유연성을 얻을 수 있습니다.

예를 들어 AWSElasticBlockStore 또는,
AzureFile, AzureDisc와 같은 클라우드 스토리지를 사용하거나,
이 매우 유연한 CSI 유형을 사용하여,
모든 종류의 스토리지를 클러스터에 연결할 수 있습니다.

중요한 것은 스토리지가 클러스터 노드에 있지 않고,
다른 곳, 예를 들어 특정 클라우드 스토리지 서비스에 있다는 겁니다.

```
<img width="816" alt="스크린샷 2023-02-21 오후 12 28 05" src="https://user-images.githubusercontent.com/73451727/220240122-8938b625-83b2-41f4-ba6c-cc5204be312c.png">
<img width="802" alt="스크린샷 2023-02-21 오후 4 37 51" src="https://user-images.githubusercontent.com/73451727/220278966-8c2a2b22-7d25-4870-994c-ff74fd430b03.png">

## 영구볼륨의 사용
```
영구볼륨을 사용하려면 먼저 영구볼륨을 정의하고 -> host-pv.yaml
영구볼륨 클레임을 정의하고 (위에서 생성한 영구볼륨과 연결 / VolumeName) -> host-pvc.yaml
해당 볼륨을 사용하려는 pod에서 클레임을 연결하면 된다. 

쿠버네티스에는 SC(스토리지 클래스) 가 있음
그리고, 디폴트 스토리지 클래스가 존재
Kubectl get sc
NAME                 PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
standard (default)   k8s.io/minikube-hostpath   Delete          Immediate           false                  20d

위의 디폴트 스토리지 클래스를 사용
host-pv / host-pic file 에 storageClassName: standard 추가

적용
Kubectl apply -f=host.pv.yaml # 볼륨 생성
Kubectl apply -f=host-pvc.yaml # 클레임 생성
Kubectl apply -f=deployment.yaml # deployment 적용
Kubectl get pv # 영구 볼륨 리스트 조회
Kubectl get pic # 클레임 조회
Kubectl get deployments

```

## 환경변수
```
# 방식 1
코드에서 process.env.STORY_FOLDER를 사용하려면, 

Deployment -> pod -> 컨테이너 구성에 env 추가
env:
 - name: STORY_FOLDER
   value: 'story'

# 방식 2
environment.yaml file -> configMap 객체 생성

kubectl apply -f=environment.yaml
kubectl get configmap

Deployment -> pod -> 컨테이너 구성에 env
env:
 valueFrom:
  configMapKeyRef:
   name: data-store-env
   key: folder
   
kubectl apply -f=environment.yaml
kubectl get deployments
kubectl get pods


```

## 네트워크
<img width="816" alt="스크린샷 2023-02-21 오후 7 53 31" src="https://user-images.githubusercontent.com/73451727/220325570-ea62ed82-92fb-415b-8779-b6edd5e1daa9.png">

```
1. service object
 - 쿠버네티스에서 네트워크를 다룰때에 쓰이는 객체

minikube status
minikube start
kubectl get deployments
kubectl get services

cd users-api
docker build -t bridgehong/kube-network-users .
docker push bridgehong/kube-network-users

kubernetes/users-deployment.yaml 생성
 kind: Deployment
 spec:
  selector:
   matchLabels:
    app: users
 template:
  ''''''''''''
kubectl apply -f=users-deployment.yaml

kubernetes/users-service.yaml 생성
 kind: Service
 spec:
  selector:
   app: users # pod 자체를 선택 (컨테이너가 아님)
  type: ClusterIP: default값, 클러스터 내부에서만 접근가능함 (외부접근 불가)
        NodePort: 노드 IP 주소를 통해, 외부에서 접근 / 노드가 여러개 되었을때 쓰기 힘들어진다.
        LoadBalancer: 로드발란서 IP 주소로 접근 / 외부에서 접근하기에 가장 효과적인 방식

kubectl apply -f=users-service.yaml
minikube service user-service -> url:port 로 접근 가능해짐

2. pod internal network
'http://${process.env.AUTH_ADDRESS}/hashed-password/'

# 같은 pod에 배포!
# 동일한 Pod에 있지만, 다른 컨테이너인 둘간의 통신을 위해 AUTH_ADDRESS 환경변수는 어떤것으로 설정해야 할까? -> localhost !
users-deployment.yaml
template:
 spec:
  containers:
   -name: users
    image: academind/kub-demo-users:latest
    env:
     - name: AUTH_ADDRESS
       value: localhost
   -name: auth
    image: academind/kub-demo-auth:latest

```

<img width="723" alt="스크린샷 2023-02-21 오후 7 41 10" src="https://user-images.githubusercontent.com/73451727/220323084-a79e7a6e-f68a-4ddb-8ebf-e7b1aa5a477c.png">
<img width="1058" alt="스크린샷 2023-02-21 오후 8 51 02" src="https://user-images.githubusercontent.com/73451727/220337702-236b3dd3-25cd-469f-a45c-057b1637ac9b.png">
<img width="1407" alt="스크린샷 2023-02-21 오후 8 52 04" src="https://user-images.githubusercontent.com/73451727/220337732-aeea9277-622c-4bfc-aa3f-3e74180ad526.png">

```
3. pod to pod network
deployment 3개 각각 생성 -> pod 분리
users-deployment.yaml
auth-deployment.yaml
tasks-deployment.yaml

service 3개 각각 생성 -> 각각 접근범위를 제한 할수 있음
users-service.yaml
auth-service.yaml
tasks-service.yaml

```





































