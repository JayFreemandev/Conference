오래전부터 이어온 Batch + Jenkins 조합의 데이터 적재 시스템(하루 5천곡 수급)이 3개월 안에 1270만곡의 노래를 적재해야 하는 상황을 맞이하며 새로 도입한 SCDF(Spring Cloud Data Flow)에 대한 이야기

### **대용량 데이터를 빠르게 처리하는 기법**

**부하분산**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f79b4545-7e84-47ef-8b17-d16bee142191/Untitled.png)

부하 분산은 데이터 처리 작업을 여러 개의 서버에 분산하여 처리하는 기법입니다. 예를 들어, 쇼핑몰에서 대용량의 주문 데이터를 처리해야 할 때, 이 작업을 한 대의 서버에서 처리하면 처리 시간이 많이 소요됩니다. 그러나 여러 개의 서버에 작업을 분산하여 동시에 처리하면 작업 처리 시간을 줄일 수 있습니다.

**파티셔닝**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4448059a-b8b6-47c5-9cf3-23c19e744388/Untitled.png)

파티셔닝은 데이터를 분할하여 여러 개의 서버에 분산 저장하는 기법입니다. 예를 들어, 대규모의 사용자 데이터를 저장하는 경우, 사용자 ID를 기준으로 데이터를 분할하여 각각의 서버에 저장합니다. 이를 파티셔닝이라고 합니다.

**워크플로 매니지먼트**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3aa1e0a9-df05-4f3c-8962-8a43e95a881f/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/153bd564-ab35-4fcb-b48e-0506c7efc01e/Untitled.png)

위 두가지 방식은 기본적이고 중요한 방법이지만 워크플로 매니지먼트가 있어야 운영에서 작업하고 처리하는 관리가 가능하다. 네이버에서는 스트림처리를 위한 워크 플로우로 카프카기반의 SCDF를 사용했다.

**어떤 상황에서 스트림처리를 해야할까?**

1. 데이터가 도착하자마자 실시간 작업이 필요한 경우
2. 데이터를 수집하거나 저장하지 않아야한다
3. 무한한 작은데이터가 지속적으로 유입되고 연속적인 처리가 필요한경우 

**상황 예시**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/824a7d6c-a17e-4c22-a0eb-ff3ab16f36aa/Untitled.png)

**네이버에서 겪은 문제**

스트림처리를 해야하는 데이터를 배치프로세스로 관리하면서 문제가 발생했다.

매일같이 오픈되는 앨범 데이터, 뮤직 비디오 파일들을 공급사로부터 요청받아서 매일 적재한다.

2019년도에 12,700,000(1,270만)곡 적재 요청 

당시 배치로는 하루에 가공해서 처리할 수 있는 데이터는 5000곡

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/85d94f8a-dd32-490b-8a4d-e7c20f86f1b4/Untitled.png)

즉 6년이 지나야 1,270만곡 적재 가능 

**배치 +  젠킨스는 충분하지않다**

대부분의 프로젝트는 잘 운영이 가능하지만 네이버 바이브의 상황에서는 충분하지 않다고 판단

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/57d1fc23-42f0-4fc7-9e2c-212a9de3b626/Untitled.png)

**Job 구조**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fbb39cb8-9cca-4600-8b0f-f24ca1065d49/Untitled.png)

1. 카프카로 컨슈밍하고 컨슈머를 늘리면 되지 않을까?

하지만 내부적으로 IO처리하는 과정에서 앨범 정보이전에 아티스트 정보등이 처리되어야하는 선행조건들이 존재해서 무작정 카프카를 소비하는것은 아니라고 판단.

1. 부하분산과 파티셔닝을 이용하면 되지 않을까?

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0556504e-172d-48a8-b597-2375b841da6b/Untitled.png)

가장 처리시간을 많이 잡아먹는 AOD job을 1개에서 10개로 늘려보고 노드수도늘려본다. 하루 5천곡에서 5만곡까지는 늘리는데 성공했지만 목표하는 천만건을 해결하기에는 부족하다.

**개선을 위해 전체적인 구조를 분석하니 아래와 같은 불편함이 존재**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3f59604f-e124-4765-9f7d-3556a74d53c1/Untitled.png)

1. track-job만 실행하고싶은데 모든 코드가 로딩이 되어야한다 

만약 다른 job에 있는 코드가 잘못된경우 내 코드에 영향을 미치게된다.

따로 job을 분리할수없기때문에 하나의 수정에도 모든 자원이 소모된다.

공통적인 커넥션을 사용하고있어서 잦은 연결/끊음이 존재

1. 워크플로 관리가 불가능

현재 job의 실행상태나 다음 job들을 알고 싶은데 데이터가 어떻게 처리되고 어떻게 흘러가는지 직접 코드를 보지않으면 쉽게 파악할 수 없는 문제

1. Job 실행도중 Lock이 걸린다면

Job 실행도중 문제가 생기면 다음 Job이 동작하지않게되고 데이터 처리가 안되게 된다. 바이브는 실시간으로 적재를 해야되는데 그날 신곡을 발표한 가수의 앨범이 안열리는 이슈들이 많았다.

**SCDF란?**

Spring Cloud Data Flow 는 Cloud Foundry 및 Kubernetes에서 스트리밍 및 일괄 데이터 처리 파이프라인을 구축하기 위한 마이크로서비스 기반 툴킷

**SCDF 특징**

- 웹 대시보드, REST API, console shell 다양한 인터페이스로 제공
- 로컬, Cloud Foundry 및 Kubernetes 위에서 설치 및 운영 가능
- SCDF의 상태 및 데이터 관리는 관계형 DB에서 관리

대쉬보드화면을 통해서 모든 조작이 이루어진. 배포, 롤백을 하고 데이터가 어떻게 흘러가는지도 확인하는데 수월했다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/adc51a8d-14b2-40c1-8725-4956ac76f35e/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8b1fe011-6374-4a58-82f4-8856c841cdc1/Untitled.png)

어플리케이션끼리 어떤 단계로 작업을 처리할건지 파이프라인을 드래그 앤 드랍으로 붙이면 하나의 작업 스트림이 완성된다. 젠킨스에서도 스텝별로 파이프라인을 연결할 수 있지만 더 세세하게 지원하지는 않는다.

**데이터 플로우 전달 방식**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9c26af12-0f1a-48f1-8796-d96b0d274899/Untitled.png)

이벤트 요청이 들어오면 Application은 Application2로 요청보내는게 아니라 카프카로 보내게된다. 카프카가 Application2로 전달하는 방식으로 이루어지는데 이럴경우 Application2에서 문제가 생겨도 그 시간동안 데이터가 유실되는게 아니라 복구되면 카프카에서 다시 전달해서 진행된다.

**매뉴얼 스케일링과 데이터 파티셔닝**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/778d19f7-c5c5-45f8-b909-e96ee20a3c2e/Untitled.png)

Aod job을 처리하는 과정을 최적화해야 처리량을 늘릴 수 있었다

SCDF에서는 스케일링을 대쉬보드에서 수동으로 늘리고 줄일수있고 

특정 단계에서만 파티셔닝을 적용하는게 가능하다.

**코드**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5b57c718-4d79-4605-bbdd-0034e5589152/Untitled.png)

**대쉬보드 설정**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/712f50fc-afce-498e-a15d-45e15f20802c/Untitled.png)

Aod 단계에서는 서버 60대를 사용해서 분산처리하고 파티셔닝 적용

**오토스케일링**

[https://youtu.be/IDH6X1pmgxc](https://youtu.be/IDH6X1pmgxc)

서버 60대씩 늘리다보면 비용 문제가 발생한다. SCDF에서는 오토스케일링을 지원하는데 프로메테우스에 규칙을 지정해서 1분에 메세지가 n건 이상 발생해서 트래픽이 증가하는 상황이거나 혹은 트래픽이 감소하는거같으면 alert를 주고받으며 컨테이너수를 자동으로 조절하게된다.

**결론**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b3642ff9-17fe-4ed1-b9cf-92396943c8d8/Untitled.png)

SCDF 도입에 3년이 걸렸고 5000→50000→500000→N곡 적재 가능

데이터가 어떻게 흐르는지 눈으로 볼 수있고 문제가 생겼을때 해당 스트림만 교체 및 개선이 가능하다. 유연하게 스케일링을 조절하고 자동으로 관리가 가능하게끔 목표를 달성했다.
