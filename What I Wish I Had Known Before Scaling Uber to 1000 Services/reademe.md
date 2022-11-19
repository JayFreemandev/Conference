
![uber](https://user-images.githubusercontent.com/72185011/202848972-8eb2743f-1f53-4356-a438-07589ce3dbe9.jpg)

2016년 기준 400개 도시, 70여개의 국가 6000+명의 직원들이 우버와 함께하고있다. 이중의 엔지니어는 대략 2000정도이다.  
****Matt Ranney****가 1년 반전에 우버에서 일을 시작했을때는 200명이니 1년 반사이10배 이상 증가한것이다.  
</br>  

**Life Lesson**

![Untitled](https://user-images.githubusercontent.com/72185011/202849003-3393277c-65ab-4b0f-9fb3-eb29da897807.png)

2년도 안되는 사이 서비스의 개수가 800개를 돌파할거같은 우상향 그래프를 볼 수 있다.   
이때 마이크로서비스의 도입을 시작했다. 각 팀들이 최고의 툴을 사용해서 신속하고 독립적으로 움직여야할 시기가 왔기 때문이다.   
</br>  

**Cost of Microservice**

거대한 마이크로서비스의 RPC로 이루어진 분산 시스템 작업을 수행하는 비용에 대해 생각하기 시작한다면 단일 설계인 모놀리식 시스템보다 작업하기가 까다로워진다.
서버 하나가 죽어버리면 어떻게 될까? 그 서버가 다른 서버에 연쇄적인 영향을 준다면 어떻게 트러블 슈팅을 할것인지에 대해 생각해야되지만 이또한 마이크로서비스를 선택함으로써 얻어가는 대가이다.   
</br>  

![Untitled 1](https://user-images.githubusercontent.com/72185011/202848977-4850eabe-f2f4-4ad8-a143-73890e4800d3.png)  
옛날 우버는 100% 아웃소싱 PHP로 이루어져있었다.   
엔지니어링팀이 들어오고나서 NodeJs와 지도와 코어쪽은 파이썬과 자바로 작성하고 다른쪽은 Go로짜고 언어의 정글에 도달했다.  
</br>  

**Language**
Hard to share code, hard to move between teams.  
→ Fragments the culture  

물론, 마이크로서비스이기에 팀들이 다른 언어를 사용하고가능했지만 그만큼의 운영에 드는 비용은 고려하지 않은것이다.     
개발자들을 재구성하고 다른 팀에 배치했을때의 또 다시 비용이 들기 시작한다.   
배울수야 있겠지만 같은 언어로 개발되지 않았기 때문에 적응까지의 시간이 든다.  
</br>  

**HTTP**

- HTTP/REST get complicated
- JSON needs a schema
- RPCs are slower than PCs

→ Server is not browser. 
</br>  

**Performance**

모든 팀들마다 다른 언어로 작성할 수 있게 했지만  언어마다 성능을 측정하는 방법이 다르기에 전체적인 시스템 성능을 이해하기에 어려웠었다.   
대쉬보드만큼은 표준을 만들어 다른 언어지만 문제가 생겼을때 쉽게 찾을수있게 동일하게 맞추어 문제를 해결하였다.  
</br>  

**Performance ve Optimaziation**

성능을 이른 시기에 최적화를 반대하는 문화가 우리 산업에서 보이는데 왜냐하면 당장 문제가 되지 않기때문이다. 
컴퓨터는 갈수록 싸지고 엔지니어들의 몸값은 갈수록 비싸지는 상황에서   
</br>  
왜냐하면 코드 최적화 잘하는 엔지니어를 고용하는것보다 값싼 컴퓨터로 서버 한대 더 늘리는게 합리적이라고 생각하기 때문이다. 
당연히 좋은것은 필수가 아니지만 “known” 인지하고 있는거는 필수적이다.  
  
지금 당장은 아닐지라도 성능과 관련된 쓴맛을 보게될거다.  
</br>

**Logging**  
![Untitled 2](https://user-images.githubusercontent.com/72185011/202848984-6e0f7352-efff-47f4-82a0-87106badbf1d.png)  
자기 멋대로 로그를 찍는 사람들이 많아져서 지속적이고 구조화된 로깅이 필요했다.   
Zap과 같은 구조화된 로그 오픈소스들을 활용해서 해결했음.  
</br>  
  
![Untitled 3](https://user-images.githubusercontent.com/72185011/202848990-cd7bcad0-6041-4d4b-ad56-a224c747ca6b.png)   
훨씬 빠르고 용량면에서도 훌륭한 성과를 보였다.  
</br>

****Load testing****

- Need to test against production
- Without breaking metrics
- Preferably all the time
- What I wish I knew: All systems need to handle “test” traffic  
</br>  

****Failure testing****

• What I wish I knew: People won’t like it  
</br>  

****Migrations****

- Old stuff still has to work, you cannot stop the world, people are making changes all the time
- What happened to immutable?
- What I wish I knew: mandates are bad. People will be more willing for change if you provide a better system rather than forcing them to change the current one  
</br>  

****Politics****

서비스가 많아지니 정치도 생기더라

Services allow people to play politics. Politics happen when “Company > Team > Self” is out of order.  
</br>  

**Conclustion**

다양한 언어와 기술을 도입한다는 유연한점이 마냥 좋은건 아닌거같다.   
많은 댓글에서 1년사이 200명에서 2000명이 된다는게 위에 문제점을 마냥 피할수는 없는 가장 큰 이유라고 언급했는데 너무 무책임하게 확장한게 아닌가 생각이 든다.  
</br>  

![Untitled 4](https://user-images.githubusercontent.com/72185011/202848995-4886e34f-3a57-45e0-bfa0-7213e810a940.png)  

기업에서 위와 같은 이슈들을 겪는다는것에 대해 의아함을 가지는 사람들도 존재했다.  
우버는 엔지니어링 팀은 전부 신입들인가, 개발 관리 문화라는게 없는가  

로그 관리와 지속적인 최적화는 선택이 아닌 필수로 느껴지고 트래픽 테스트같은 경우는 개인 프로젝트가 아닌 업무에서 직접 경험하기는 아직 힘든거같다.  
