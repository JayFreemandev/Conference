# 그런 REST API로 괜찮은가?

**REST가 어떤 계기로 나왔는가 ?**

91년도 World Wide Web이 나왔을때 어떻게 인터넷에서 정보를 공유 할 것인가? 모든 정보들을 하이퍼링크로 연결해서 전송하자.
</br>  

**API 역사**

![Untitled](https://user-images.githubusercontent.com/72185011/202478133-0ab38b4c-c820-46ad-8df5-e170e1c0994e.png)


세일즈포스가 세상의 첫 API를 공개했지만 사용하기 복잡한 이유로 외면되었다.   
추후 REST라는 2000년도 논문을 인용한 API가 나오고 06년 AWS에서 자사 API 사용량의 85%가 REST인것을 언하기 시작했다.  
위에 REST는 Roy Fielding의 박사학위 논문에서 네트워크 아키텍쳐 스타일을 설명하기 위해 만들어진 용어이다.   
즉 분산 하이퍼미디어 시스템, 웹을 위한 아키텍쳐 스타일인것이다.  
</br>  

**REST API란?**

REST 아키텍쳐 스타일을 따르는 API가 REST API다.  
</br>  
 
**아키텍쳐 스타일이란?**

제약 조건의 집합이라고 보면된다. 즉 이 제약조건들을 모두 지켜야 REST를 따르는것이라고 할수있다.   
</br>    

**REST를 구성하는 스타일**  

- clinet -server
- stateless
- cache
- **uniform interface**
- layered system
- code on demand

대부분의 조건들은 HTTP를 충족시키면 잘 지켜지지만 **uniform interface는 잘 지켜지지 않는다.**  
</br>  

**uniform interface의 제약조건**

- identification of resources
- manipulation of resources through representations
- **self-descriptive messages**
- **hypermedia as the engine of application state (HATEOAS)**

마지막 두가지는 오늘날의 REST API가 지키지 못하고있다.  
</br>  

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
</br>    

**HATEOAS**

애플리케이션의 상태는 Hyperlink를 이용해 전이되어야한다.

![appli](https://user-images.githubusercontent.com/72185011/202478156-a17bc183-acd3-4444-9f97-28cccfb7cc61.jpg)
해당 페이지의 링크를 따라가며 상태가 전이 되었기에 HATEOAS하다.    
</br>    

**uniform interface는 왜 필요한가?**

- 서버의 기능이 변경되어도 클라이언트를 업데이트할 필요가 없다.  
서버와 클라이언트는 독립적으로 진화하며 REST의 계기인 “How do I imporove HTTP without breaking the Web.”을 충족시키기 위해서다.  
웹 브라우저는 REST를 매우 잘 만족하고있기에 웹 페이지 변경했다고 웹 브라우저를 업데이트 할 필요가 없다.    
</br>   

**REST API 제약조건 몇개 안지켜도 되는거 아닌가?**
하이퍼텍스트를 포함한 self decriptive한 메세지의 uniform interface를 통해 resource에 접근하는 API를 REST API라고 말하면 제약조건을 지켜야한다. -Roy Fielding-
SOAP에 비해 REST가 단순하고 규칙이 적고 쉬워보이는건 착각이였다. 규칙이 많고 굉장히 어렵다.   
</br>   

**내가 만드는 API가 꼭 REST 지켜야되나 너무 어렵다**

![REST](https://user-images.githubusercontent.com/72185011/202478173-0dac8681-08ff-4e95-aab9-5cc17a7835eb.jpg)
시스템 전체를 통제할수있다고 생각하거나 REST 생태계의 관심없다면 REST에 대해 따지느라 시간 낭비하지마라. -Roy Fielding-  
</br>   

![REST_API](https://user-images.githubusercontent.com/72185011/202478192-72ec9068-916c-4ecc-be3d-78ecf1759b54.jpg)
1번을 파고들어보면 웹은 REST가 잘되는데 왜 API는 REST가 잘 안되는지에 대해 질문을 준다.

웹페이지 경우 미디어 타입이 페이지인 HTML이지만 API는 JSON이다.   
HTML에서는 <a> 태그를 이용해서 하이퍼링크를 걸수있지만 JSON을 할 수 없다.  
또한 Self descriptive를 HTML 명세로 남길수있지만 JSON에는 방식이 불완전하다.  

JSON은 Self descriptive와 HATEOS를 만족시키지 못한다. 그렇다면 이 두가지가 독립적인 진화에 어떤 도움을 주는가?  

Self descriptive → 확장 가능한 커뮤니케이션   
서버나 클라이언트가 변경되더라도 오고가는 메세지는 언제나 sd 하므로 해석이 가능하다.  

JSON의 SD를 보완하는 방법

```java
HTTO/1,1 200 OK
Content Type : application/json
Link: <httpsL//example.com/docs/blabla>, rel="profile"

{
	{"id" : 1, "title" : "자바  ORM"}
}
```

키와 값이 어떤것을 의미하는지에 대한 link를 남김으로써 sd를 보완할수있다.    
단점은 클라이언트가 link 헤더와 profile을 이해해야한다는 단점이 있다.  
</br>  
	
HATEOS → 애플리케이션 상태 전이의 late biding  
어디서 어디로 전이 가능한지 미리 결정되지 않는다.     
서버에서 링크 바꾼다고 클라이언트에서 동작유연하게 바뀐다. 어떤 페이지로 동작후 다음 동작을 할 수있다는점도 가지고있다.   

JSON의 HATEOS를 보완하는 방법  
![Untitled 1](https://user-images.githubusercontent.com/72185011/202478248-623f2d76-b606-4f8e-9f1b-2658eaded5b7.png)
혹은 Link나 Location 헤더를 통해 표현하는 방법도 가능하다.   
</br>   

**공식 REST 사이트에 Media Type 등록이 필수인가?**
NO 하지만 하면 좋다. 회사에서 모두가 알고있다면 할 필요 없다.  
</br>   

**Conclusion**
오늘날 대부분 REST API는 REST를 따르지 않는다(Self descriptive와 HATEOS), REST는 긴 시간에 걸쳐 진화하는 웹 어플리케이션 설계를 목적으로 만들어졌다.   
본인이 긴 시간에 걸쳐 진화하는 웹 어플리케이션을 만들어야한다면 REST를 따라 REST API를 설계하면 된다. 따르지 않는다면 HTTP API라고 부르는게 맞다.   
하지만 REST API라고 부를 수는 있다. 설계자인 Roy가 매우 싫어할것이다. 트위터에도 블로그에도 니가 만든건 REST API가 아니라고 열띤 의견을 공유해주고 있다.  
마이크로소프트 오픈 소스에도 HTTP API로 고치라고 Pull Request를 보냈지만 마이크로소프트는 틀린거 알지만 REST API라고 부를거라고 알을 박았다.  
