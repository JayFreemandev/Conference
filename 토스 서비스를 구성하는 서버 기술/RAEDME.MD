# 토스ㅣSLASH 21 - 토스 서비스를 구성하는 서버 기술

![Untitled](https://user-images.githubusercontent.com/72185011/172847936-6054e7fc-e680-4334-905a-c9d6c2b624f0.png)

토스는 누적 사용자 1,800만 이상. 40여 개의 서비스가 돌아가는 모바일 금융 플랫폼이다. 토스는 매달 천만 명 이상 사용자를 어떻게 처리할까?

![Untitled 1](https://user-images.githubusercontent.com/72185011/172848039-3f979fb4-cd41-4c29-ab7f-ad149e9f9fc9.png)

두개의 데이터 센터와 AWS 일부 서비스를 이용중

데이터 센터가 두 개다보니 컴포넌트 운영 이슈가 생김

![Untitled 2](https://user-images.githubusercontent.com/72185011/172848062-15dfd15f-178c-4823-b704-1a99dee91e92.png)

MSA로 구성이 되어있어 많은 서버가 올라간다. 효율적 서버 관리를 위해 

- 쿠버네티스 컨테이너 오케스트레이션 도입,
- 칼리코 CNI
- Service Mesh로 iSTIO를 사용.
- 민감자료 저장 용도로 Ceph를 사용해서 내부 스토리지 서비스 제공

![Untitled 3](https://user-images.githubusercontent.com/72185011/172848087-12ac20a4-4e3a-4d85-b6a3-3fc8cea58f89.png)

예전에는 Memcached를 캐시로 많이 사용했지만 **데이터보존 문제**와 **optimistic lock 구현 편의성**때문에 

- Redis Cluster를 캐시로 더 많이 사용중이다.

카프카는 로그 데이터 파이프라인으로 사용하는 **로그용 클러스터** 하나

서비스에서 **메세지 큐**로 사용하는 클러스터 하나를 운영중

로깅과 모니터링으로 아래와 같은 기술들 사용 

```java
elk + filebeat, thanos, grafana
```

![Untitled 4](https://user-images.githubusercontent.com/72185011/172848434-044f4f94-994c-4431-b3a3-72ea8e487749.png)


송금말고도 채팅이나 카드사 알림이나 대출 서비스처럼 **외부 연동 서비스가 많다**. 여러 서비스 특성에 맞춰 스프링을 사용중이고 최근 개발되는 소스들은 코틀린을 사용하여 개발중이다.

![Untitled 5](https://user-images.githubusercontent.com/72185011/172848452-c84d194f-358c-4af6-9859-72ae8796d752.png)


AWS는 DNS설정을 하거나 이미지 검수, static file 서빙 기능으로 사용중

# 데이터 센터 트래픽 조절

## 2개의 데이터 센터를 이용한 안정적 서비스 운영

![Untitled 6](https://user-images.githubusercontent.com/72185011/172848461-66eb50c6-0138-438d-8fda-ce69378f9e75.png)


토스는 데이터 센터 2개를 운영하면서 평상시에는 트래픽이 50대 50인데

한쪽이 100%가 되는 트래픽을 옮기는 작업을 자주한다. 왜냐하면 두가지 

이유인데. 

- 장애가 났을때 문제를 해결해야 복구되는 상황이라면 장애가 나지않는 데이터센터로 트래픽을 이동시키면 장애 대응 시간이 감소
- 새로운 시스템 도입이나 쿠버네티스 설정 변경시 물론 테스트를 많이하지만 문제가 일어날 가능성이 0%는 아니다. 트래픽을 한쪽으로 싹 옮기고나서 작업을하고 1%씩 옮겨본다 점진적으로 늘려가고 문제가 생기면 바로 트래픽을 제거해 장애를 겪는 고객 수를 최소화

서비스 카나리 배포를 데이터 센터에 적용했다고 보면 된다.

트래픽 이동은 L7에서 바로 이동할수있어서 주사용. 

라우터 53에서 조절할 경우 변경 사항이 다른 라우터에 전파된 후에 반영

되다보니 시간이 오래 걸림.

# K8S + Istio

![Untitled 7](https://user-images.githubusercontent.com/72185011/172848485-7bda316d-7a59-43b9-9b73-9a4595e5b355.png)


19년도에 dc/os에서 쿠버네티스로 갈아타기로 결정

Container Orchestration에서 가장 중요한게 

- Service Discovery
- container lifecycle management

DC/OS에서 카나리 배포로 사용하던 vamp는 service discovery 기능의 비효율과 한계가 존재

![Untitled 8](https://user-images.githubusercontent.com/72185011/172848506-a7f21be0-a8dd-4409-93b5-dd2a97f877f3.png)


쿠버네티스 도입시 Istio도 함께 도입 왜?

- Service Mesh의 장점이 크다고 판단

모놀리틱에서 MSA로 넘어가면서 여러 서버들의 호출로 하나의 서비스가 구성된다. 

서버들 간의 네트워크 처리가 필요하였고 

- circuit breaker
- retry
- fallback

등 애플리케이션에서 처리 Istio도입하면 Istio-proxy가 sidecar 형태로 붙어서 모든 네트워크를 proxy한다. 

proxy하면서 어플리케이션이 하는 일을 대신할수있어서 어플리케이션 언어와 관계없이 infra차원에서 한번에 해결해 줄 수 있는 장점. 

내부 서비스간 요청이 많은 MSA의 특성상 중앙집중 형태로 처리하다 보면 

- 장애에 취약
- 부하가 많음

또한 client side에서 처리할 수있는 점이 매력 

DC/OS보다는 쿠버가 넓은 생태게와 더 큰 서비스 규모에서 검증도 매력

하지만 좋은점도 있었지만 아닌점도 있더라.

![Untitled 9](https://user-images.githubusercontent.com/72185011/172848541-edc491a7-b489-4019-b6ef-c70a617c5820.png)


Istio는 서비스에 Sidecar로 붙어서 실행되고 iptable을 통해 모든 트래픽 제어

Istio proxy를 통해 나나고 Istio proxy로 통해 받는다. 

Istio proxy는 **envoy proxy**를 Istio에서 wrapping한것이다.  

envoy의 탄생배경 “**프록시가 어떻게 동작하는지 명확하게 보여주마**”  따라서 

connection pool 동작방식이나 프록시를 보내는쪽, 나가는쪽을 모아보면 **어디서 connection 끊기는**

**지 문제를 쉽게 파악**한다 

기존 tcp dump로 보내는쪽, 받는쪽 모두 dump로 확인했던걸 envoy가 대신 해결해준다고 보면 된다. 

따라 토스 **서비스의 전체적인 Observability 대폭 증가**

![Untitled 10](https://user-images.githubusercontent.com/72185011/172848667-3f4e484e-9098-42c2-8878-ed25895d9add.png)


Istio를 사용하면서 infra쪽으로 네트워크 처리를 넘기려 시도

- circuit breaker는 host별 설정이라 세부설정이 힘들다.
- retry는 api 트랙잭션 처리 여부와 응답값에 따라 retry 시도 여부가 갈린다.

세부 설정이 가능할지라도 손이 많이가서 어플리케이션에서 처리

대신 mTls설정을 통해  A서비스는 B를 호출하고, C는 B가 안되는 서비스별 권한 적용

Envoy 필터를 작용해 어플리케이션 변경없이 다이나믹하게 네트워크 기능을 추가할수있는 장점

Envoy 필터로 헤더처리 접근제어를 하는중

![Untitled 11](https://user-images.githubusercontent.com/72185011/172848690-ce716e70-a5aa-45b0-bb0a-e5e99077df79.png)


인스턴스 단위로 카나리를 하면 세밀한 트래픽 조절이 힘들다.

EX) 10개의 인스턴스에서 1개만 카나리를 나가면 10% 카나리, 

1% 카나리를 배포하려면 100개의 인스턴스가 필요.

Istio는 router의 weight로 카나리 가능

인스턴스 개수와 상관없이 1% 카나리 가능.

![Untitled 12](https://user-images.githubusercontent.com/72185011/172848745-4416df30-46c8-4ba2-86d9-26f25e0cb744.png)


Istio의 또다른 기능으로는 failure injection test와 squeeze test

- failure injection test

특정 조건에 맞는 요청일 경우 실패 또는 응답을 늦게 하도록 만들어 

서비스에서 실패에 대한 처리가 되도록 되는지 테스트

- squeeze test

하나의 인스턴스에 요청을 높여 부하를 주는 테스트, 

서비스가 어느 정도 요청부터 부하를 느끼는지 배포를 통한 성능 하락이 있는지 미리 알수있다.

# API-GATEWAY / WEBFLUX

![Untitled 13](https://user-images.githubusercontent.com/72185011/172848768-e22b7e63-74e1-47bd-99bd-43d739dad4ec.png)


클라이언트 인증과 암복호화 담당 서비스가 있었는데 트래픽 증가로 고사양 요구 

따라서 신규 gateway 도입 검토

- zuul
- kong
- spring cloud gateway

## spring cloud gateway 선택 이유는?

WebFlux도입하며 reactor 운영 자신감, 성능 검증

api gateway는 트래픽을 여러 필터를 통해 처리하게 되는데 커스텀 필터를 만들어서 구현 

스프링의 내부코드가 reactor이지만 유지보수의 편리성을 위해 코루틴 사용

## gateway 관리

시간이 갈수록 하나로 처리하던 게이트웨이도 무거워지기 시작

넷플릭스에서는 gateway를 개발하면서 어느 팀이 gateway를 관리할지

공통로직은 어떻게 처리 할지 짱구를 많이 굴림

토스는 **각 팀의 PR를 받아 플랫폼팀에서 승인하는 구조**로 관리중

**공통로직은 모듈화**하여 처리중이다.

![Untitled 14](https://user-images.githubusercontent.com/72185011/172848821-8bc8316c-385f-4db5-a854-4d6ed2979032.png)

많은 서비스들이 MVC로 되어있지만 홈탭이나 내 소비와 같이 여러 데이터를 모아 보여주는 

- I/O가 많은 프로젝트를 WebFlux를 이용.

**초기에 reactor로 개발했는데 러닝커브가 너무 높아 코루틴 도입**

# Monitoring

![Untitled 15](https://user-images.githubusercontent.com/72185011/172848842-94db7bbd-a4a7-4e34-aeff-29671dc98f41.png)


## 모니터링은 오픈소스를 적극 활용

MSA구조상 서버들이 여러 곳에서 올라오기 때문에 Log의 중앙집중화 필요성 느낌

**logback과 filebeat을 통해** 어플리케이션 로그를 **kafka**로 보내고  **kibana**에서 검색. 

로그에는 메세지를 포함한 다양한 정보가 들어감. 컨테이너 ID와 서비스ID가 들어가서 어느 데이터

센터에서 어떤 컨테이너가 로그남긴지 확인 이걸로 특정 장비 문제인지 특정 서비스의 문제인지 구

분 

배포ID는 어느 배포 버전에서 발생하는지 구분하여 신규 배포 문제인지 이전배포 문제인지 확인 배

포에서 생긴 문제는 바로 롤백해버린다. 

MSA 구조에서는 문제가 발생했을 때 원인이 요청을 받는 서비스 일수도있고 요청을 주는 서비스에

서 잘못 줄수도있어서 어디 문제인지 확인하기위해 Request ID를 쌓고 apm으로는 **pinpoint**사용중

인데 해당요청이 pinpoint의 어떤 요청과 매칭되는지 확인용으로 pinpoint ID도 같이 쌓고있다.

금융회사로 장기보관하는 데이터들은 카프카에서 **하둡으로 데이터를 장기보관,** 

로그는 데이터센터급 장애가 발생해도 서비스 개발자가 확인할수있어야되서 이중화로 나눔

![Untitled 16](https://user-images.githubusercontent.com/72185011/172848895-ad403216-ac81-45d0-ada6-32f654537daf.png)


Metric은 쿠버와 잘맞는

- **프로메테우스를** 사용중

HA와 데이터 장기보관을 위해 

- **thanos**를 사용중

데이터센터별 THANOS에 쿼리를 하기 위해 

- **global thanos** 따로 사용

**ceph**를 내부 저장소로 사용하고 **grafana**로 데이터를 확인

grafana에 있는 대시보드와 알림 데이터들도 중요해서 저장소로 사용하는 mysql은 서비스 db와 마

찬가지로 이중화 되어있다.

![Untitled 17](https://user-images.githubusercontent.com/72185011/172848908-a7e56efb-3b78-4ea0-b002-257335b81bea.png)


알림은 개별 서비스에서는 **sentry**로 알림을 설정

전체적인 관리는 es로그를 활용하는 프로젝트를 만들어 사용 + slack 

grafana에도 알림걸어서 아래와 같은 상황 발생시 slack 알림

- http error code
- istio error flag

flink를 통해 서비스들의 응답시간을 파싱해서 알림을 보냄

- 응답시간이 튀거나
- 응답 실패율이 높아졌을 시

**다양한 알림을 통해 시스템 문제 상황을 파악**

# Kafka

![Untitled](%E1%84%90%E1%85%A9%E1%84%89%E1%85%B3%E3%85%A3SLASH%2021%20-%20%E1%84%90%E1%85%A9%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%AE%E1%84%89%E1%85%A5%E1%86%BC%E1%84%92%E1%85%A1%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%89%202d1e7ca6ed27447d887901d83363a44f/Untitled%2018.png)

## 데이터센터가 두개인데 어떻게 카프카를 안정적 운영 고민

카프카에 자체적으로 있는 HA기능, 하나의 클러스터로 두 개의 데이터센터를 묶을까?

아니면 다른 클러스터로 구성해 Replication을 걸까?

우버에서 사용하는 방식을 보고 두 개의 클러스터로 운영하는게 좋다 판단 

- 데이터센터급 장애 발생시 다른 센터에 있는 서비스에서 일부 장애 발생 가능성.
- 두 개로 운영시 다른 데이터 센터에서는 장애가 발생하지 않아 트래픽을 옮기면 빠른 복구 가능

**다만 두개의 클러스터 운영시 추가적인 기능이 필요**

ACTIVE/STANDBY인 CONSUMER가 다른 데이터 센터로 ACTIVE하게 넘어갈때 두 개의 카프카는 

CONSUMER OFFSET이 다르기 때문에 어느 지점부터 CONSUME을 할지 모르게 된다. 

**두 개의 KAFKA OFFSET을 SYNC해주는 어플리케이션이 필요**해서 

데이터플랫폼팀에서 와쿠와쿠라는 이름으로 개발해서 사용중

PRODUCER는 ACTIVE/ACTIVE라 장애시 추가 대응은 필요 없고

CONSUMER는 ACTIVE/STANDBY로 평상시 한족 데이터센터의 KAFKA를 바라보다 

장애시 반대편 데이터센터 KAFK를 바라보게해서 장애 대응

![Untitled](%E1%84%90%E1%85%A9%E1%84%89%E1%85%B3%E3%85%A3SLASH%2021%20-%20%E1%84%90%E1%85%A9%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%AE%E1%84%89%E1%85%A5%E1%86%BC%E1%84%92%E1%85%A1%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%89%202d1e7ca6ed27447d887901d83363a44f/Untitled%2019.png)

## CONSUMER에서 메세지 처리를 실패하는 부분을 어떻게 할까?

우버에서 사용하는 토픽별 Retry , Dead Letter Topic을 본떠서 

토스에서도 Spring Kafka를 Wapping해서 구현. 

같은 토픽에서 Retry 로 처리시간이 오래 걸리면 뒤에 있는 메시지 처리가 밀리기 때문에 

다른 토픽에서 Retry 를 하는것이 성능적 이점. 

Requeue를 할때 필연적으로 따라오는것이 Delay다.

Consume하는 서비스가 정상화가 안됬는데 Requeue를 하면 다시 에러가 발생하기 떄문. 

Topic이 나눠져아 Retry 시 Delay를 주는 것도 자유롭다.

토스에서는 토픽별로 자동 Delay Requeue를 설정할 수 있고 

Dead Letter Topic에서는 수동배치로 Requeue하고 있다.

![Untitled](%E1%84%90%E1%85%A9%E1%84%89%E1%85%B3%E3%85%A3SLASH%2021%20-%20%E1%84%90%E1%85%A9%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%AE%E1%84%89%E1%85%A5%E1%86%BC%E1%84%92%E1%85%A1%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%89%202d1e7ca6ed27447d887901d83363a44f/Untitled%2020.png)

## PRODUCER에서는 KAFKA A가 실패할 경우?

Kafka B로 보내서 Retry를 하도록 처리

**여러 종류의 Kafka를 운영중이라 서로 백업도 오케이**

Produce는 대부분이 재발행을 바로 하길 원해서 약간의 Delay후 자동으로 Requeue를 시도한다. 자동 Requeue도 실패하면 Dead Letter Topic 으로 이동해 수동배치로 Requeue시도 

메세지 발행은 DB처리 후 이벤트 발행용으로 많이 사용중. 

DB와 카프카 이기종 트랙잭션에 대한 고민을 해결하기 위해 위 처럼 **KAFAKA CLUSTER** 이중화 대응 

하지만 여전히 하나의 트랜잭션이 아니라 완벽한 트랜잭션 필요한 서비스 경우

OUTBOX 패턴을 활용해 동일 트랙잭션 처리 후 Retry 를 통해 전송하는 방법을 사용

# Redis

![Untitled](%E1%84%90%E1%85%A9%E1%84%89%E1%85%B3%E3%85%A3SLASH%2021%20-%20%E1%84%90%E1%85%A9%E1%84%89%E1%85%B3%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%AE%E1%84%89%E1%85%A5%E1%86%BC%E1%84%92%E1%85%A1%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%80%E1%85%B5%E1%84%89%202d1e7ca6ed27447d887901d83363a44f/Untitled%2021.png)

## 예전에는 멤캐시 사용하지만 지금은 Redis cluster를 사용

cluster라서 데이터 보존이 좀 더 용이해 장기보관이 필요없는 **단발성 데이터 저장**과 **캐시**로 사용중.

현재 데이터센터가 2개밖에 없어서 한쪽에 master가 많은 상황

데이터센터간 네트워크가 끊겼을때 **split brain**이 발생해 양쪽 다 마스터가 되는 상황 가능성 존재

인프라 이중화로 대응중이고 3번째 데이터센터 준비중

데이터센터가 두개라 한쪽이 장애시 반대편에서도 마스터 3대, 슬레이브 3대를 구성하기 위해 각 각 

6대씩 총 12대로 하나의 클러스터를 구성

3번째 데이터센터 준비시 마스터 1대 슬레이브 2대를 각 데이터 센터별로 구성해 9대로 클러스터 구

성 예정 운영하다보면 메모리 확장도 필요 그때마다 **slot rebalance**로 대응중이고 aws에서는 분산락

으로 **redlock**을 구현해 이용하고 있다. 3번째 데이터센터가 완성되면 redlock을 적용할 예정

**Redis client로는** 

- redisson
- jedis
- lettuce

## 왜 lettuce를 선택?

webflux와 mvc에서 공통사용이 가능하고 스프링에서 지원하는 lettuce도입

# 마치며

금융 기업보다는 인터넷 기업과 비슷한 구조

명확한 문제 정의가 선행된 기술 도입, 신기술 막 도입 안한다

여러 회사에게 배우며 지속해서 성장할 예정

우버최고.
