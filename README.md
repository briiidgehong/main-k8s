## k8s
```
Kubernetes, also known as K8s, 
is an open-source system for 
automating deployment, 
scaling, 
and management of containerized applications.
```
## 수동배포 대신 관리형 서비스를 이용하는 이유 = ECS 컨테이너 관리의 이점
<img width="836" alt="스크린샷 2023-01-31 오후 6 46 19" src="https://user-images.githubusercontent.com/73451727/215725447-30514696-71a3-4622-8a04-eedf46adce07.png">

## ECS를 사용할때의 단점 
#### AWS에 구속된다. -> AWS에서 구성한대로 사용해야한다.

## 쿠버네티스란?
<img width="927" alt="스크린샷 2023-01-31 오후 7 00 00" src="https://user-images.githubusercontent.com/73451727/215728702-50dc1891-41ba-4f5e-8307-8eba01105842.png">
```
(기본적으로 ECS와 비슷한 역할을 한다.)
(클라우드 프로바이더와는 별개로)
배포하는 방식, 
컨테이너 스케일링,
컨테이너가 다운될 경우 모니터링하는 방법,
컨테이너를 교체하는 방법을 정의한다.

쉽게 쿠버네티스 구성을 작성할 수 있다.
원하는 배포를 정의하는 구성 파일, 
배포할 컨테이너, 
인스턴스 수,
스케일을 확장해야 하는지, 
교체해야 하는지 등을 설정
```

## 구성파일 작성후 특정 셋업, 툴을 이용하여 프로바이더에 전달
<img width="1175" alt="스크린샷 2023-01-31 오후 7 05 56" src="https://user-images.githubusercontent.com/73451727/215730133-8fc9588b-6e41-4157-9e20-f814e819db82.png">
```
쿠버네티스의 배경 아이디어
-> "배포를 설명하는 표준화된 방식을 갖는 것"

다른 클라우드 프로바이더와 함께 사용하려는 경우,
클라우드 프로바이더에 특화된 구성을 제거하거나,
새로운 클라우드 프로바이더로 교체하기만 하면 되며,
전체 구성 파일을 다시 작성할 필요가 없습니다.

```


