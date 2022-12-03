# DDD

**Domain Driven Design**

what domain driven design actually is so this is my attempt at to me this is an approach to **designing software that puts the domain at the center and that seems to be something that
should be completely obvious this should be the only way to build software** 

기술적 측면보다 도메인 지식을 강조하는 소프트웨어 설계 방법 ****Stefan Tilkov****이 내린 정의이다.   
도미노 피자 서비스라면 피자가 도메인이 될것이고 우리가 어떤문제를 해결할것인가 그것이 곧  도메인의 주체가 된다.

Domain Languge의 창시자인 *Eric Evans*는 DDD에 대해 다음과 같이 정의한다.  ![Untitled](https://user-images.githubusercontent.com/72185011/202837255-3923b14a-46ef-44a9-b8a7-1e1169da0286.png)
 
DDD is approach to the development of complex software in which we : 

1. focus on the core domain
2. explore models in a creative collaboration of domain practitioners and software practitioners
3. speak a ubiquitous language within an explicitly bounded context
<br>

**Ubiquitous Language**

개발자와 비개발자가 모두 알아 들을수있는 모델을 기반으로한 언어를 사용하는것이 첫번째 핵심 원칙이다.   
프로젝트에 참여한 사람들이 서로 이해하지 못한다면 프로젝트를 성공적으로 이끌 수 없다.

구성원간의 의사소통외에도 설계 모델링이나 UML작성, 해당 설계를 바탕으로한 코드의 클래스명과 메소드명을 정하기때문에 유비쿼터스한 언어로 도메인의 의도를 정확하게 반영하고 전달해야한다.

![Untitled 1](https://user-images.githubusercontent.com/72185011/202837260-b7798379-6d2c-4748-9dd0-4e8d3b6716af.png)

**Tactical Pattern**

Layered Architecture, Value Object, Aggregate, Domain Events, Factories, Repositories, Entity

도메인 전문가 및 기술팀이 함께 모여 유비쿼터스 언어를 통해 도메인 지식을 공유 및 이해하고 이를 기준으로 개념과 경계를 식별해 바운디드 컨텍스트(bounded context)로 정의하고 경계의 관계를 컨텍스트 맵(context map)으로 정의.  
<br>
  
**Strategic design**

Context, Model, Ubiquitous Language, Bounded Context, Context Map, Damain Model

전략적 설계에서 도출된 바운디드 컨텍스트와 도메인을 이용하여 애그리거트 패턴, 엔티티와 값 객체, 레포지토리, 등을 구성하고 구현.  
<br>

**Should design be domain-driven?**

DDD가 실제로 원하는 목적이 뭘까, 소프트웨어와 연관된 부분들을 연결하여 진화하는 모델을 만들어 복잡한 어플리케이션을 쉽게 만들어 가는 것이다.   
<br>

Loose Coupling  → 가벼운 설계

소프트웨어의 위기와 커뮤니케이션 문제를 해결하기 위해 탄생하였다.

전체적인 강연의 흐름은 구체적인 예시보다 처음 들어보는 개념들로 장점을 설명하며 30분이 흘렀는데 좋은 내용을 정리하기에는 상당히 많은 반발들이 존재했기에 중립을 지키면서 내가 이해한 개념을 정리하려고한다.  
<br>

**Microservice**

> "마이크로서비스는 여러 개의 작은 서비스 집합으로 개발하는 접근 방법이다. 각 서비스는 개별 프로세스에서 실행되고, HTTP 자원 API 같은 가벼운 수단을 사용해서 통신한다. 또한 서비스는 비즈니스 기능 단위로 구성되고, 자동화된 배포 방식을 이용하여 독립적으로 배포된다. 또한 서비스에 대한 중앙 집중적인 관리는 최소화하고, 각 서비스는 서로 다른 언어와 데이터, 저장 기술을 사용할 수 있다.”
-마틴 파울러-
> 

마이크로서비스는 응용 프로그램을 세분화된 서비스 모음으로 기능적으로 분해하는 모듈식 전략이다. 대규모의 정교한 서비스를 설계할 때 마이크로 서비스는 매우 중요하고 대부분은 이제 한 사람이나 한 팀이 관리하기에는 너무 복잡하다. 개발자 팀은 애플리케이션을 빌드하고 이해할 수 있는 모듈로 세분화해야 한다.

각 마이크로 서비스는 위반하기 어려운 Bounded Context를 가지고 있다. 마이크로서비스는 마이크로서비스간의 상호의존성을 독립적으로 피하기 위해 높은 응집력과 낮은 결합력을 가져야 한다. 마이크로 서비스를 식별하는 것은 도메인, 트랜잭션 경계 및 잘 정의된 단일 목적에 대한 확실한 이해가 필요하기 때문에 말처럼 쉽지않다.


![Untitled 2](https://user-images.githubusercontent.com/72185011/202837263-288946ef-f110-4d02-a33c-90854ba11203.png)

MSA를 구성하는 필수 개념인 loose coupling과 high cohension또한 DDD의 핵심 요소중 하나이기에 서로 얽혀있으며 함께 사용된다.  **도메인들 간에는 Loose Coupling하고 도메인 내에서는 High Cohesion 해야 한다.**

도메인을 잘게 나누는 것, 즉 Loose Coupling 시키는 것만이 능사가 아니라, 어떤 서비스들을 하나의 도메인으로 잘 묶어서 High Cohesion 하게 할지 설계하는 것까지가 DDD나 MSA가 추구하는 지향점이 되어야 한다.

**DDD is not silver bullet**

DDD는 복잡한 프로젝트에서 유용하게 쓰이지만 일반적인 플랫폼 개발같이 기술적이고 비지니스 상호 작용이 적은 프로젝트는 DDD에 적합하지 않다.

설계론이기 보다 철학에 가깝기에 깊게 이해하지 않으면 러닝 커브가 높고 잘못 사용한 DDD는 오히려 안쓰는것만치 못하기때문에 높은 이해도를 요구한다.

MSA와  DDD는 지향하는 핵심 요소가 같기에 함께 설명되고있고 그저 하나의 설계 디자인중 하나이다. 다른 비판적인 글들을 정리해보면 DDD는 애자일과 같은 철학이기 때문에 유행어도 많고 아이디어도 불명확하고 실행도 정확하게 뭘 하라는건지 확실하게 이해할 수없었다.

**YAGNI 위반**

**YAGNI(**You aren't gonna need it)는 [프로그래머](https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8)
가 필요하다고 간주할 때까지 기능을 추가하지 않는 것이 좋다는 [익스트림 프로그래밍](https://ko.wikipedia.org/wiki/%EC%9D%B5%EC%8A%A4%ED%8A%B8%EB%A6%BC_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)(XP)의 원칙이다.[[4]](https://ko.wikipedia.org/wiki/YAGNI#cite_note-XPA-4) 익스트림 프로그래밍의 공동 설립자 [론 제프리스](https://ko.wikipedia.org/w/index.php?title=%EB%A1%A0_%EC%A0%9C%ED%94%84%EB%A6%AC%EC%8A%A4&action=edit&redlink=1)는 다음과 같이 썼다: "실제로 필요할 때 무조건 구현하되, 그저 필요할 것이라고 예상할 때에는 절대 구현하지 말라

왜냐하면 개발자가 많은 레이어를 만들어야 하고 코드를 읽기 힘들게 되기 때문이다. 많은 개발 시간을 요구한다.

DDD에 대한 프레젠테이션을 가진 빅테크 회사는 잘 없다. DDD는 컨설턴트들 사이에서 인기가 있는데, 왜냐하면 새로운 것을 팔아야 하기 때문이다.  프레젠테이션에서는 모든 것이 좋아보인다.

"유비쿼터스 언어"라는 용어조차 명확하지 않다.  그리고 DDD에 관해 대표적인 책들은 새로운 용어들로 가득 차 있다. 모든 페이지에 새로운 용어가 등장한다.

DDD를 비판할 때, 옹호자들은 DDD가 팀 역학, 프로젝트 관리, 비즈니스 우선주의에 관한 것이라고 말하는데. 나한테는 DDD는 용어로 가득 찬 모호한 개념이고  많은 마케팅과 컨설턴트들이 좋아하는것이라고 밖에 생각이 들지 않는다. 페이스북, 구글, 아마존, 애플, 트위터의 어떤 팀이 DDD를 사용해서 좋은 결과를 얻을 때까지, 나는 눈여겨 보지 않을거다.

**Conclusion**

DDD란 무엇이고 어떤것을 추구하는 설계 디자인인지 어떠한 장점을 가지고 이에 상응하는 단점은 무엇인지에 알아봤다. 컨퍼런스 강연치고 내용이 부실하기도 했고 외부에서 더 많은 토론 자료들을 찾을 수 있었다. 

중립적으로 장/단점을 가져와서 나열했지만 단점에 대한 목소리에 주장이 더 크게 실리기도한다. 최근 들어 애자일과 관련된 프로젝트 관리, 컨설팅쪽의 신드롬들이 소프트웨어에 많이 녹아있는것을 피부로 느끼고있고 막대한 장점보다는 

![Untitled 3](https://user-images.githubusercontent.com/72185011/202837268-6b12c776-8d18-4a0b-9171-afb3a4991825.png)


도메인 알러지같은 느낌이 더 강하게 들었다. 뭐든 잘 쓰면 좋겠지만은 컨설턴트들의 새로운 프로그램들보다는 기본에 충실하자라는게 매번 느낀 결론이였다. 애자일이든 DDD든 여기 저기 스며들어있는 하나의 industry다. 

정말 DDD를 깊이 있게 공부한 팀에서 일을 해보고 느끼지않는 이상 프레젠테이션이나 강연을 보더라도 크게 공감할수없을거같다. 

reference

[https://www.youtube.com/results?search_query=DDD+is+overated](https://www.youtube.com/results?search_query=DDD+is+overated)
