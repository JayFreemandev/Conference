REST가 어떤 계기로 나왔는가 ?

91년도 World Wide Web이 나왔을때 어떻게 인터넷에서 정보를 공유 할 것인가? 모든 정보들을 하이퍼링크로 연결해서 전송하자.

API란?

세일즈포스가 세상의 첫 API를 공개했지만 사용하기 복잡한 이유로 외면되었다. 추후 REST라는 2000년도 논문을 인용한 API가 나오고 06년 AWS에서 자사 API 사용량의 85%가 REST인것을 언하기 시작했다.

위에 REST는 Roy Fielding의 박사학위 논문에서 네트워크 아키텍쳐 스타일을 설명하기 위해 만들어진 용어이다. 즉 분산 하이퍼미디어 시스템, 웹을 위한 아키텍쳐 스타일인것이다.

REST API란?

REST 아키텍쳐 스타일을 따르는 API가 REST API다.

아키텍쳐 스타일이란?

제약 조건의 집합이라고 보면된다. 즉 이 제약조건들을 모두 지켜야 REST를 따르는것이라고 할수있다. 

REST를 구성하는 스타일

- clinet -server
- stateless
- cache
- **uniform interface**
- layered system
- code on demand

대부분의 조건들은 HTTP를 충족시키면 잘 지켜지지만 **uniform interface는 잘 지켜지지 않는다.**

**uniform interface의 제약조건**

- identification of resources
- manipulation of resources through representations
- **self-descriptive messages**
- **hypermedia as the engine of application state (HATEOAS)**

마지막 두가지는 오늘날의 REST API가 지키지 못하고있다.

**self-descriptive messages**

```java
GET / HTTP /1,1 
-> X, **self-descriptive messages 하지 않는다.**

GET / HTTP /1,1 
Host: www.example.com
-> O 
```

```java
HTTP/1.1 200 OK
{{ "op" : "add"}}
-> X, **self-descriptive messages 하지 않는다.**

HTTP/1.1 200 OK
Content-Type : application/json
{{ "op" : "add"}}
-> Δ

op가 뭔지, 어떤 HTTP인지 클라이언트가 받고 한눈에 알지못한다.
반쪽짜리 **self-descriptive messages** 
```

**HATEOAS**

애플리케이션의 상태는 Hyperlink를 이용해 전이되어야한다.
