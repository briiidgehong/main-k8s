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

## 즉, 쿠버네티스는 멀티플 머신 배포용 도커 컴포즈 라고 보면 된다.
```
도커 컴포즈 툴은 우리의 로컬 머신에서 다중 컨테이저 프로젝트를 쉽게 관리하는데 도움이 됩니다.

그리고 쿠버네티스는 다중 머신 설정에 대해서도 동일한 작업을 수행합니다.

애플리케이션을 배포할 때 하나의 머신이 아니라,
여러 컴퓨터, 여러 머신에서 애플리케이션을 실행하여
이를 수행하기 때문입니다.

그리고 쿠버네티스는 컨테이너를 배포하고,
여러 머신에서 자동으로 매우 쉽게 컨테이너를 모니터링을 하고 재시작 합니다.

따라서 다중 머신 설정에서
도커화되고 컨테이너화된 애플리케이션을 관리하고
실행하기 위한 몇 가지 편리한 배포 특정 부가 기능이 있는
도커 컴포즈와 같습니다.

그것이 결국 쿠버네티스가 하는 일이죠.
쿠버네티스는 배포용 docker-compose와 같습니다.
```

## 쿠버네티스 아키텍쳐
![screenshot-www udemy com-2023 02 13-20_27_01](https://user-images.githubusercontent.com/73451727/218445911-1c8525ef-3b6a-43fc-bb41-8d78f54a926a.png)
```
# POD
쿠버네티스는 포드(Pod)라는 것에 의해 관리됨
포드는 쿠버네티스 세계의 가장 작은 단위
포드를 쿠버네티스를 위한 구성 파일에서 정의할 수 있습니다.

# CONTAINER
포드는 컨테이너를 보유합니다.
포드는 항상 함께 작동해야 하는 여러 컨테이너를 보유할 수 있지만
가장 작은 단위는 단순히 하나의 포드이며,
그런 다음 컨테이너를 실행할 책임이 있어 이 컨테이너를 실행합니다.

# WORKER NODE
내부에 컨테이너가 있는 이 포드는 소위 워커(worker) 노드에서 자신을 실행합니다.
워커 노드를 머신, 가상 인스턴스라고 생각할 수 있습니다.

따라서 배포 섹션에서 본 AWS 에서 EC2 인스턴스는 워커 노드가 되는 겁니다.
워커 노드는 특정 양의 CPU와 메모리가 있는 컴퓨터이며 그 머신에서 포드를 실행할 수 있습니다.
그리고 하나의 워커 노드는 둘 이상의 포드을 실행할 수 있습니다.

# PROXY / CONFIG
그리고 프록시는 쿠버네티스 상에서 네트워크 트래픽의 제어를 설정하는 또다른 도구입니다.
프록시는 기본적으로 이러한 포드가 인터넷에 연결할 수 있는지의 여부와
이러한 포드 및 그 내부에서 실행되는 컨테이너를 외부 세계에서 어떻게 접근할 수 있는지를 제어합니다.
예를 들어, 이러한 포드의 컨테이너에서 웹 애플리케이션을 실행하는 경우
사용자의 외부 트래픽이 이 컨테이너에 도달할 수 있도록 프록시를 구성해야 합니다.

# THE CONTROL PLANE
큰 애플리케이션의 경우 일반적으로 다른 포드를 실행할 수 있는 둘 이상의 워커 노드가 존재합니다.
모든 컨테이너를 실행하는데 충분한 컴퓨팅 성능을 갖기 위해
둘 이상의 서버가 필요할 수 있으며, 이는 컨테이너 스케일링을 포함합니다.

트래픽이 들어오고, 감소함에 따라 컨테이너 및 포드를 동적으로 추가 및 제거하는 경우
포드는 쿠버네티스에 의해, 사용 가능한 모든 워커 노드로 자동으로 배포됩니다.

따라서 여러 워커 노드에서 서로 동일한 컨테이너를 실행하여 워크로드를 고르게 분배할 수 있습니다.
이제 이러한 모든 워커 노드와 그 노드에서 실행되는 포드 및 컨테이너를 어떻게든 제어해야 합니다.
누군가는 이러한 컨테이너와 포드를 만들고 시작해야 하며, 실패하거나 더 이상 필요하지 않은 경우, 
이를 교체하거나 종료해야 합니다.
그리고 그것은 마스터 노드에 의해 특별히 호출되는 컨트롤 플레인(plane)에 의해 수행됩니다.
즉, 이것은 기본적으로 워커 노드와 상호 작용하여 제어하는 ​​컨트롤 센터입니다.

따라서 쿠버네티스로 작업할 때,
일반적으로 워커 노드 또는 포드과 직접 상호작용하지 않습니다.
그렇게 할 수는 있어도, 일반적으로 그렇게 하지 않습니다.
대신 쿠버네티스와 이 컨트롤 플레인이 이 역할을 대신합니다.

그리고 여러분은 개발자로서 원하는 종료 상태를 정의하면,
쿠버네티스가 그를 고려할 겁니다.

# MASTER NODE
마스터 노드는 단순히 다른 서버, 다른 리모트 머신입니다.
마스터 노드는 마스터 노드에서 실행 중인 이 컨트롤 플레인을 갖고 있으며,
워커노드와 워커노드 상에서 실행중인 포드과 상호작용하는 책임을 가집니다.

이론적으로 마스터 노드이자, 유일한 워커 노드 역할을 하는
하나의 머신을 가질 수 있지만,
더 큰 배포의 경우에는 고가용성을 보장하기 위해
자체적으로 여러 머신에 분할될 수 있는 마스터 노드가 있고
워커 노드는 다른 인스턴스, 마스터 노드와 독립적인 다른 머신이 됩니다.
따라서 하나의 워커 노드가 다운되어도 마스터 노드가 함께 다운되지는 않습니다.
마스터 노드의 이 컨트롤 플레인은 실제로는 마스터 노드에서 실행되는 다양한 서비스의 다양한 도구 모음입니다.

# 클라우드와의 연계
이 모든 것이 클러스터를 형성합니다.
이것은 마스터 및 워커 노드의 클러스터를 형성하고,
따라서 이러한 모든 부분이 연결된 하나의 네트워크를 형성합니다.
그런 다음 마스터 노드는 클라우드 프로바이더(AWS) API에 명령을 보내,
그 클라우드 프로바이더에게 클라우드 프로바이더의 특정 리소스를 생성하고
원하는 큰 그림, 이 최종 상태를 그 클라우드 프로바이더에 복제하도록 지시합니다.

AWS에 대해 생각해보면,
AWS가 필요할 수도 있는 로드 밸런서와 이 네트워크를 갖고
쿠버네티스를 갖기 위해 필요한 모든 EC2 인스턴스를 AWS에서 생성하도록
AWS와 상호 작용할 수 있습니다.
AWS가 필요한 모든 EC2 인스턴스,
필요할 수 있는 로드 밸런서와
이 네트워크를 갖는데 필요한 기타 모든 것을 생성합니다.

그리고 마스터 노드인 EC2 인스턴스에서
쿠버네티스와 쿠버네티스 툴을 실행하기 위해
그런 다음 이 네트워크에 속한 다른 EC2 인스턴스를 제어하여
이러한 포드에서 컨테이너를 실행합니다.

```

## 쿠버네티스는 클러스터(EC2)를 생성하지 않는다.
![screenshot-www udemy com-2023 02 13-20_47_52](https://user-images.githubusercontent.com/73451727/218450133-a87f00c1-6f2a-454f-8c13-3348a612740b.png)

```
쿠버네티스를 사용하기 위해 사전에 해야 할 일이 있습니다.
쿠버네티스를 실행할 수 있는 특정 환경을 제공해야 합니다.

그리고 개발자나 관리자로서 여러분이 해야 할 중요한 것은
클러스터와 워커 노드,
그리고 쿠버네티스가 설치되고, 작업을 수행하는
마스터 노드를 만들고 설정해야 한다는 겁니다.

이 노드에 모든 쿠버네티스 소프트웨어를 설치해야 합니다.
즉, 클러스터의 일부인 이러한 머신에서 말이죠.
그리고 쿠버네티스에 필요한 이러한 모든 것을 구성해야 합니다.

사용 중인 클라우드 프로바이더에 따라
쿠버네티스 클러스터에 필요한 부가 리소스를 설정해야 할 수도 있습니다.
로드 밸런서나 특정 파일 시스템 서비스 처럼요.
클러스터를 준비해야 한다는 사실만 알고 있으면 됩니다.


로컬 개발과 AWS 예제로
클러스터 생성을 하는 특정 도구와 서비스가 있습니다.
쿠버네티스가 이러한 리소스를 설정하지 않는다는 점을 이해하는 것이 중요합니다.
대신 쿠버네티스는 이러한 리소스를 사용합니다.
설치 및 설정이 완료되면, 쿠버네티스가 이러한 포드를 관리합니다.

즉, 이러한 것들, 컨테이너를 포함하는 이러한 유닛.
그것을 생성하고,
사용 가능한 리소스를 활용하기 위해 자동으로 배포합니다.
모니터링하고 실패하면 다시 시작합니다.
실행 중인 배포를 관리하구요.
원활하게 실행되어 작동 상태를 유지하며 이 모든 것을 관리합니다.
여러분을 위해 컨테이너를 시작할 겁니다.

앞서 말했듯이 쿠버네티스는
컨테이너를 배포하기 위한 Docker Compose와 비슷합니다.
Docker Compose가 로컬 컴퓨터를 구성하지 않는 것처럼,
쿠버네티스는 로컬 컴퓨터에 도커를 설치하지 않습니다.
로컬 컴퓨터를 전혀 설정하지 않습니다.

Docker compose가 로컬에서 하는 것처럼
쿠버네티스는 로컬 컴퓨터를 사용하여 컨테이너를 시작합니다.
또한 쿠버네티스는 리모트 머신에서 이를 수행하기 위해
특정한 환경이 필요합니다.

쿠버네티스는 클러스터를 생성하지 않습니다.
그냥 사용할 뿐입니다.

```

## 워커노드
<img width="890" alt="스크린샷 2023-02-15 오후 6 02 06" src="https://user-images.githubusercontent.com/73451727/218981806-5d35a7ca-ff1b-4434-9c1b-b3e00b77e8fd.png">

```

앞서 언급한 것처럼, 워커 노드는 단순히 우리의 머신에서 실행 중인 ec2 인스턴스입니다.
그리고 워커 노드는 설명한 것처럼 마스터 노드에서 관리합니다.

워커 노드 내부에 포드가 있습니다.
그리고 포드는 하나 이상의 애플리케이션 컨테이너와
이러한 컨테이너에 속한 모든 리소스를 호스팅합니다.

컨테이너를 올바르게 실행하기 위한 구성뿐 아니라 볼륨과 같은 것도 리소스에 들어가 있죠.
그리고 포드 자체는 이 마스터 노드에 의해 즉, 쿠버네티스에 의해 관리됩니다.
따라서 쿠버네티스는 예를 들어 포드를 생성하거나 삭제할 수 있습니다.

그리고 포드가 삭제되면, 포드는 내부적으로 포드에 속한 컨테이너를 실행하고 관리할 수 있습니다.

물론 하나의 포드에 하나의 컨테이너가 있지만,
밀접하게 작동해야 하는 여러 컨테이너가 있는 경우에는
포드 내부에 여러 컨테이너를 가질 수도 있습니다.

그런 다음 추가 리소스가 필요할 수도 있습니다.
예를 들어 볼륨, 즉 컨테이너가 통신할 수 있는 하드 드라이브의 일부 공간이죠.
그리고 언급했듯이, 주어진 워커 노드에 둘 이상의 포드가 실행되는 게 일반적입니다.
그건 다른 포드의 복사본일 수 있습니다.

예를 들어, 스케일링하고
하나의 동일한 컨테이너에서 여러 인스턴스를 실행하여
들어오는 트래픽을 분산하려는 경우에 대비합니다.

그러나 완전히 다른 작업을 수행하기 위해
내부에 완전히 다른 컨테이너가 있는 포드일 수도 있습니다.
워커 노드는 태스크에 특정된 것이 아니기 때문이죠.
워커 노드는 바로 여러분의 컴퓨터, 여러분의 머신입니다.
이를 염두에 두는 것이 정말 중요합니다

워커 노드는 인터넷 어딘가에 특정량의 CPU와 메모리가 있는 머신일 뿐입니다.
따라서 우리 자체 개발 머신에서 완전히 다른 컨테이너를 실행할 수 있는 것처럼,
당연히 완전히 다른 컨테이너와 태스크를 실행할 수 있습니다.

'docker run' 명령으로 다양한 이미지를 기반으로 하여
컨테이너를 원하는 만큼 실행할 수 있습니다.
그리고 우리는 실제로 'docker-compose'를 사용하여 이 과정을 진행했었죠.

백엔드, 프론트엔드, 데이터베이스 컨테이너가 모두 로컬 머신에서 실행되었었습니다.
그것이 기본적으로 워커 노드와 동일합니다.
로컬 머신에만 있지만,
클라우드 프로바이더가 제공하는 인터넷의 컴퓨터에 있는 거죠.

이제 이 워커 노드에는 이러한 포드뿐 아니라, 몇 가지 추가 소프트웨어도 있습니다.
예를 들어 도커를 거기에 설치해야 합니다.
당연히 도커는 컨테이너를 생성하고, 실행하는 포드에 필요하기 때문이죠.

그런 다음 기본적으로 워커 노드와 마스터 노드 간의 통신 장치인
kubelet이라고 하는 또다른 소프트웨어가 실행되고 있습니다.
즉, 기본적으로 그 머신, 워커 노드 머신에서 실행중인 소프트웨어 서비스입니다.

그것은 실제로 마스터 노드와 통신을 하죠.
마스터 노드가 이 워커 노드의 포드를 제어할 수 있도록 합니다.
그리고 실행중인 이 프록시 서브를 가집니다.

이는 들어오고 나가는 트래픽을 처리하는 책임을 가지며,
모든 것이 계획한대로 작동하도록 합니다.
허용된 트래픽만이 포드에 접근하게 하고,
허용된 트래픽만이 워커 노드를 떠날 수 있게 하죠.
이것이 워커 노드입니다.

워커 노드와 그곳에서 실행 중인 것을 자세히 살펴보았습니다.
가장 좋은 점은 쿠버네티스를 사용하여 원하는 최종 상태를 정의하기만 하면 됩니다.

그런 다음 AWS와 같은 클라우드 프로바이더를 사용하는 경우
AWS는 이 쿠버네티스 정의를 제공할 수 있는 서비스가 있습니다.
그러면 AWS가 모든 인스턴스를 설정하고 필요한 모든 소프트웨어를 설치합니다.

여러분은 그걸 처리할 필요가 업죠.
단지 그곳에서 무슨 일이 일어나고 있는지 알기만 하면 됩니다.
개발자는 항상 코드와 구성이 수행하는 것이 무엇인지 알아야 하기 때문이죠.

```

## 마스터노드
<img width="696" alt="스크린샷 2023-02-15 오후 6 02 53" src="https://user-images.githubusercontent.com/73451727/218981966-29874c98-873e-4574-b277-d2a79339f3b6.png">


```
마스터 노드 내부에서 가장 중요한 것, 가장 중요한 서비스,
가장 중요한 소프트웨어라고 할 수 있는 것, 구동되고 있는 것은 API 서버입니다.

이것은 워커 노드에서 실행되는 kubelet 서비스에 대한
카운터 포인트인 마스터 노드 머신에서 실행되는 간단한 서비스입니다.
즉, 이것은 워커와 마스터 노드 간의 통신을 위한 카운터 파트(대응자)입니다.

이 API 서버 외에도 마스터 노드 머신에 설치되어 실행되는
또다른 서비스는 기본적으로 포드를 관찰하고,
새 포드가 생성되어야 하는 워커 노드를 선택하는 일을 담당하는
스케줄러입니다.

포드가 비정상적이 되거나, 다운되었거나,
또는 스케일링으로 인해, 새로운 포드를 생성해야 하기 때문에
새 포드를 추가해야 하는 경우에 말이죠.

즉, 이것은 실제로 워커 노드에 무엇을 알려야 하는지
API 서버에 알리는 역할을 합니다.

그리고 큐브 컨트롤러 매니저 (Kube-Controller-Manager)라는
또다른 서비스를 가지고 있습니다.
이 서비스는 워커 노드 전체를 감시하고 제어하며
적당한 수의 포드를 가동 중에 있는지 확인하는 역할을 합니다.
그러므로 스케줄러 및 API 서버와 긴밀하게 연동됩니다.

그리고 큐브 컨트롤러 매니저의 특정 버전이 있습니다.
클라우드 컨트롤러 매니저는 동일한 작업을 수행하지만,
클라우드 프로바이더에 따라 다릅니다.
그런 다음, AWS 또는 Microsoft Azure와 같은
클라우드 프로바이더에게 무엇을 해야 하는지 알려줍니다.

즉, 이 클라우드 컨트롤러 매니저는
AWS, Azure, 사용 중인 프로바이더가 무엇이든,
그에게 명령을 번역합니다.

어쨌든 실제로 사용할 가능성이 매우 높은 대형 클라우드 프로바이더는
이미 여러분과 여러분이 있는 곳에서
모든 것을 가져갈 수 있는 서비스를 가지고 있으므로
쿠버네틱스 구성을 제공하기만 하면 모든 힘든 일을 처리합니다.
(역자주: 마스터와 워커 노드를 수동으로 생성하고, 이들 서비스를 모두 설치하지 않아도 된다는 의미)

```

## 용어정리
<img width="699" alt="스크린샷 2023-02-15 오후 6 12 55" src="https://user-images.githubusercontent.com/73451727/218984293-a40f6609-ed5e-4c16-a3c1-f3a88a0e01ca.png">

```
# 클러스터
노드 머신, 마스터, 워커 노드,
배포 혹은 원하는 최종 상태를 구성하는 모든 것의 컬랙션 세트입니다.

# 노드
하나 또는 여러 개의 포드를 호스팅하는 특정 하드웨어 용량을 가지며
클러스터와 통신하거나, 클러스터 내에서 통신하는
물리적인 머신 또는 가상 머신입니다.

모든 워커 노드를 걸쳐 포드를 관리하는 컨트롤 플레인를 가진 마스터 노드가 있습니다.
또한 다른 노드 타입으로 워커 노드도 갖고 있죠.
그리고 이들은 포드를 호스팅하는 실제 머신이며,
결국 앱 컨테이너와 이러한 컨테이너에 필요한 리소스를 실행합니다.



# 포드
포드는 컨테이너를 감싼 껍질(shell)입니다.
결국 컨테이너를 시작하여 그 특정 컨테이너를 관리하죠.
그리고 언급한대로 포드 자체는 마스터 노드에 의해 관리됩니다.
그리고 포드가 생성되는 것은 포드에서 컨테이너를 실행하는 것과 같습니다.

포드가 컨테이너를 실행한다고 말했던 것은,
결국 이 포드가 내부적으로 'docker run' 명령을 실행한다는 것을 의미합니다.

# 서비스
이전에 논리적 세트라고 알려드렸는데요.
결국 고유한 포드 및 컨테이너에 독립적인 IP 주소를 가진 포드 그룹입니다.
서비스는 포드에 접근하는데 중요하며, 따라서 그 안에 있는 컨테이너도 중요합니다.

즉, 서비스는 이 섹션에서 언급한 프록시와 관련이 있습니다.
그리고 서비스는 쿠버네티스 세계에서 특정 포드를 외부 세계에 노출하여
특정 IP 주소 또는 도메인으로 특정 포드에 연결할 수 있도록 하는 용어일 뿐입니다.

```

## 개발자가 할일 / 쿠버네티스가 할일
## "애플리케이션이 필요한 인프라에 관심을 두지 않습니다."
![screenshot-www udemy com-2023 02 13-20_47_52](https://user-images.githubusercontent.com/73451727/218450133-a87f00c1-6f2a-454f-8c13-3348a612740b.png)

```
이제 실제로 뛰어들어, 쿠버네티스 작업을 시작하기 위해
이해하고 염두에 두어야 할 정말, 정말, 정말 중요한 한 가지가 있습니다.
그리고 그것은 개발자와 관리자로서 여러분이 해야 하는 작업과
쿠버네티스가 여러분을 위해 해주는 작업 사이의 분리입니다.
쿠버네티스가 수행하는 작업과 수행하지 않는 작업을 이해하는 것이 중요합니다.

그리고 쿠버네티스가 하지 않고, 대신 여러분이 해야만 하는
한 가지 매우 중요한 작업이 있습니다.

그것은 클러스터와 노드 생성입니다.

쿠버네티스는 최종 아키텍처를 정의하는 올인원 도구이므로
클러스터, 마스터 노드, 워커 노드 구성을 정의하고
쿠버네티스가 이러한 모든 리소스를 생성할 거라 예상할 수 있습니다.

하지만 그것은 쿠버네티스가 하는 일이 아닙니다.
쿠버네티스는 포드의 모니터링, 포드 컨테이너의 모니터링,
실패한 포드 교체 및 포드의 스케일링 등, 이러한 포드를 관리하는데 도움을 줍니다.

포드 내부에 있는 이러한 컨테이너를 오케스트레이션하고
워커 노드 간에 컨테이너를 이동하고
모든 것을 구동하여 최종 사용자가 접근할 수 있도록 하는 것,
그것이 쿠버네티스가 하는 일입니다.

즉, 쿠버네티스는 실행되어야 하는 원래 방식으로
컨테이너와 그에 관련된 모든 것들이 실행되고,
애플리케이션이 실행되도록 하기 위해,
배포된 애플리케이션을 관리하는 역할을 합니다.

쿠버네티스는 애플리케이션이 필요한 인프라에 관심을 두지 않습니다.
쿠버네티스는 인프라에 대해 아무것도 모르며,
쿠버네티스는 클라우드 인프라 생성 도구나 그와 유사한 것이 아닙니다.

쿠버네티스는 클라우드 서비스 프로바이더가 아니며,
클라우드 서비스 프로바이더의 특정 서비스도 아닙니다.
대신 컨테이너화된 애플리케이션에 대한 배포를 설정할 수 있는
프레임워크이며, 개념과 도구의 모음입니다.

그러므로 쿠버네티스는
구체적인 리모트 머신의 구체적인 가상 인스턴스,
또는 작업하려는 컴퓨터에 대해 전혀 알지 못합니다.

따라서 여러분은 이러한 머신을 미리 생성해야 하고,
여러분이 생성해야 하는 이 마스터와 워커 노드에서
지난 모듈에서 언급한 쿠버네티스 API 서버, kubelet 및
이러한 모든 다양한 서비스를 설치해야 합니다.

즉, 여러분은 모든 인스턴스 생성, 그 인스턴스 관리 및
모든 소프트웨어 설치를 수행해야 합니다.

그리고 자체 데이터 센터를 운영하고 있다면
전체 데이터 센터를 관리하고, 모든 것을 준비해야 하므로 쿠버네티스가 필요한 거죠.

클라우드 프로바이더를 사용하는 경우,
워커와 마스터 노드에 필요한 모든 EC2 인스턴스를
AWS를 사용하여 생성해야 하고,
로드 밸런서 또는 EFS, 탄력적 파일 시스템 같은
추가 리소스도 생성해야 합니다.

AWS는 도커화된 애플리케이션에 필요한 것을 제공합니다.
물론 이는 우리가 원하지 않았던 수동 배포로 다시 돌아가게 만들죠.
그로 인해 우리는 다양한 단점을 직면하게 됩니다.

우리는 이러한 인스턴스와 시스템을 업데이트하고,
운영 체제 업데이트를 유지하고,
전체 네트워크 보안 그룹 항목을 관리해야 합니다.

즉, 이러한 인스턴스와 모든 시스템의 보안을 보장해야 합니다.
실패한 컨테이너 등을 교체할 때, 더 많은 문제에 직면할 수 있습니다.

좋은 소식은 이러한 문제들이 정확히 쿠버네티스가 관심을 갖는 부분이라는 겁니다.

보안 및 이러한 시스템 관리, 인스턴스 및 그 안의 운영 체제 관리,
그 모든 것을 최신 상태로 유지하는 것과 관련하여서는
쿠버네티스가 여러분을 도울 수 없습니다.

왜냐면 쿠버네티스는 서버 관리 도구가 아니고,
클라우드 프로바이더도 아니며, 관리 서비스도 아니기 때문이죠.
이런 서버에서 발생하는 것들에 도움이 될 뿐입니다.

즉, 쿠버네티스는 실제로 포드를 생성하고,
포드에서 컨테이너를 실행하는데 도움이 되며,
모니터링 및 교체에 도움이 되고,
스케일링에 도움이 됩니다.

결국 쿠버네티스는 단순히 생성한 리소스를 활용하므로,
워커 노드와 생성한 머신을 활용하고,
컨테이너에 대한 배포 목표를 달성하는데 도움이 된다고 할 수 있습니다.

쿠버네티스는 여러분을 위해 자원을 생성하지 않을 겁니다.
그것은 여러분이 여전히 해야 할 일입니다.
여러분이 숙련된 서버 관리자라면, 이 모든 작업을 수행할 수 있겠죠.

쿠버네티스가 배포 관리에 필요한 모든 인프라를 생성하는데 도움이 될 수 있지만,
쿠버네티스의 일부분은 아닌, 쿠버네티스용으로 구축된 Kubermatic과 같은 추가 도구를 가지고 있지 않다면,
이것은 예를 들어 AWS에서 이러한 리모트 머신과
이러한 인스턴스를 만드는데 도움이 되는 추가 도구가 될 겁니다.
수동으로 모든 것을 만들고 싶지 않다면 도움이 되는 도구죠.

하지만 Kubermatic은 쿠버네티스의 일부는 아닙니다.
쿠버네티스는 실제로 이러한 컨테이너를 관리하고
애플리케이션을 계속 실행하고 스케일링하는 것에 관한 것이기 때문입니다.

그 차이를 이해하는 것이 정말 중요합니다.
또한 AWS와 같은 클라우드 프로바이더에는
AWS EKS, Elastic Kubernetes Service와 같은
전용 서비스가 있어, 실제로 도움이 됩니다.

그것은 여러분의 자체 쿠버네티스 구성을 불러오는 걸 허용하는 서비스입니다.
이전에 사용해야만 했던 AWS 특정 구성인 ECS를 사용하지 않아도 됩니다.
대신 EKS를 사용하는 거죠.

즉, EKS, 탄력적 쿠버네티스 서비스는
쿠버네티스 구성으로 필요한 모든 리소스 또한 설정합니다.
그것이 제가 방금 보여드린 바로 Kubermatic 같은 서비스 또는 도구입니다.

이는 그림을 완성하는데 도움이 되며,
이 슬라이드의 오른쪽에서 처리해야 하는 단계를 제거하는데 도움이 됩니다.

쿠버네티스가 필요한 모든 머신도 생성할 것이라고 생각하기 쉽거든요.
쿠버네티스가 kubelets와 API 서버, 스케줄러,
그리고 우리가 마스터 및 워커 노드를 분석할 때 이야기한
모든 것을 설치할 거라 생각하기 쉽습니다.
쿠버네티스가 그러지 않는다는 것을 이해하는 것이 정말 중요합니다.

쿠버네티스는 어떤 머신이나 가상 인스턴스도 생성하지 않으며,
소프트웨어를 설치하지도 않습니다.

실제로는 이러한 포드와 컨테이너를 모니터링하는 관리에만 관심을 가지며,
스케일링과 접근/도달 여부에만 관심을 가집니다.
쿠버네티스는 애플리케이션의 구동과 그 실행을 유지합니다.

애플리케이션에 필요한 리소스를 생성하려면,
여러분이 직접 수행하거나,
Kubermatic과 같은 도구 또는 EKS와 같은 관리형 서비스를 사용해야 합니다.

```


















