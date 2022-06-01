Becoming a better developer by using the SOLID design principles

# SOLID 디자인 원칙의 목적

- 유지보수
- 쉬운 확장성
- 코드를 보다 읽기 쉽고 이해하기 쉽게

Introduced by Robert Martin.
![Untitled](https://user-images.githubusercontent.com/72185011/171400660-a7e9be45-2050-4058-99c7-29d77e68736e.png)


- 한 클래스는 하나의 책임만을
- 모든것을 위한 장소가 있고 모든것은 제자리가 있다.
- 바꿀 이유 하나를 찾아서 모든 것을 클래스에서 빼라(가볍게)
- 아주 작은 클래스들은 정교한 네이밍을, 큰 클래스에는 제네릭한 네이밍을

밥 아재는 단일책임원칙을 깨끗하고 잘 정리된 방에 종종 비유한다.

![abc38c1b8b7f7744771662024b4f0bba](https://user-images.githubusercontent.com/72185011/171400688-c9791239-f0dc-494f-bb06-3126d976242a.gif)

엄마가 당신의 더러운 방을 보았을때 제발 모든 물건은 제자리에 놔둬라는 잔소리를 한다. 그 물건들은 각자 있어야할 위치가 존재한다. 단일책임원칙의 핵심인것이다. 

![Untitled 1](https://user-images.githubusercontent.com/72185011/171400726-0bba9df4-f6c5-4971-a258-08cf61d3cbd1.png)


위 코드는 컨트롤러에서 가져온 간단한 스토어 메소드이다. 시작 부분에서 벨리데이션 검사를 하고 DB에 저장한후 마지막에 클라이언트에 json으로 응답을 반환한다. 

컨트롤러가 제어해야하는 작업은 플로우만 제어하는것이다. 입력을 받고

출력을 해주고 하지만 위 예시에는 백그라운드에서 일어나는 너무 많은 

내용을 알고있다.  하나의 책임만을 남겨두고 나머지는 외부로 다 이동시

켜야된다.

 ![Untitled 2](https://user-images.githubusercontent.com/72185011/171400742-c2500906-f2b3-4a1e-a534-ad9eaf8dee08.png)


유효성 검사하는 부분을 따로 분리했다 다음으로 해야할거는 받은 값들을 데이터베이스에 저장하는 부분이다. 그러면 컨트롤러는 DB에 어떻게 처리해야하는지 몰라도 작업을 수행할수있다.

![Untitled 3](https://user-images.githubusercontent.com/72185011/171400751-5fccfa85-efbf-46b9-86b8-0714ed963876.png)

사용자 레코드를 생성할 create 메소드를 만들어 DB작업을 수행한다.

![Untitled 4](https://user-images.githubusercontent.com/72185011/171400767-5527104f-1e1f-400c-b112-e56e3f0a34e2.png)

10줄에서 2줄이 되어버렸다. 전에는 값들을 벨리데이션하는 과정이 있었지만 이제는 store 함수는 input validation에 대해 몰라도된다. 그 뒤에 일어나는 create의 과정또한 몰라도된다. 호출한 값에 맞는 리턴결과만 남겨둘뿐이다.

![Untitled 5](https://user-images.githubusercontent.com/72185011/171400780-94b6a333-551d-49e0-b5a9-38f6da625a78.png)

- 확장에는 열려있으나 수정에는 닫혀있어야한다.
- 기존 코드를 변경하는 대신에 새 코드를 추가하여 기능 확장해라
- 동작을 분리해라 그럼 시스템은 쉽게 확장될수있고 망가지지 않는다.

![Untitled 6](https://user-images.githubusercontent.com/72185011/171400799-6d15c669-7cfd-4226-ab5c-2504549c66e6.png)


코드한번 봐라, 이커머스 결제 프로세스 메소드다. 입력한 유형에 따라 신용카드면? 신용카드 결제하고 아니면 페이팔로 결제한다. 하지만 일반적으로는 훨씬 다양하게 결제할수있다. 배달의 민족만봐도 카카오페이, 토스, 현금 결제, 핸드폰 결제 엄청 많다.

![Untitled 7](https://user-images.githubusercontent.com/72185011/171400814-b4e11d9c-eba1-4b06-b512-1db69e385d60.png)

![Untitled 8](https://user-images.githubusercontent.com/72185011/171400821-d137a4cc-28b0-4a28-8f93-d9126ca1fa28.png)

계속 해서 결제방식이 없을때까지 계속 이렇게 줄줄이 늘어날거다. 

여기어 Open closed 원칙을 위배하게된다 왜 일까? 클래스가 너무 빈번하게 변경되어 결코 안정정적일수가 없기 때문이다. 지금은 예시가 작고 크게 신경써야할 필요가 있나 생각이 들지만 굉장히 로직들이 타이트하게 결합되어있어서 하나가 변경된다면 모든것들에 영향을 줄수있다.

이를 해결 하기 위해서 클라이언트의 호출에따라 자동으로 팩토리 패턴으로 상황에 맞게 대응할수있다. 따라서 코드를 작성할 떄 어떤 메서드를 호출할지 이해할 필요가 없어진다. 

![Untitled 9](https://user-images.githubusercontent.com/72185011/171400838-41c64338-733d-4a68-a01f-7b8478908bae.png)


모두 같은 결제 메소드를 인터페이스로 내려 받고 상황에 맞는 방식으로 흘러간다. 

![Untitled 10](https://user-images.githubusercontent.com/72185011/171400852-1c64d182-6eb0-4877-b9cd-3b7bc3d26643.png)

![Untitled 11](https://user-images.githubusercontent.com/72185011/171400860-f82acb79-1661-49f4-8324-908b6a3f70ec.png)


컨트롤러 입장에서는 값만 받아서 던져주고 결과를 리턴해준다.

내부적으로 클라이언트는 안에서 무슨 구체적인 내용이 일어나는지 전혀 알 필요가 없다. payment라는 객체를 계속 변경하고 추가하는 대신에 새로 그냥 팩토리패턴으로 확장시켜서 처리하는걸 볼수있다. 변경이 없다.

바바 리스코프 아재의 치환법칙(Subsititution)

![Untitled 12](https://user-images.githubusercontent.com/72185011/171400874-e5639c20-8c14-4608-97b2-c9ef13575de7.png)


하위 타입으로 갈아 끼워도 클라이언트가 눈치 채지 못해야된다.

![Untitled 13](https://user-images.githubusercontent.com/72185011/171400888-8e06e19c-1cf5-4126-ae48-39e5205dd656.png)


전기 베터리 오리하고 찐 오리하고의 리스코프 치환원칙 짤이다. 

오리처럼 생겼고 오리처럼 꽥 할수있지만 베터리가 필요하다면? 

너 잘못 추상화 한거야

![Untitled 14](https://user-images.githubusercontent.com/72185011/171400901-d2f703b6-5185-49d4-9543-a99df6535e38.png)


여기서 찐 오리를 상속받은 전기 베터리 오리 클래스가 있다. 

사람이 오리를 꽉 쥐면 꽥 해야되는데 전기 오리는 사람이 쥐어짠다고 울부짖지않는다. 오리는 날지만 전기 오리는 날지도 못하고 사람이 오리를 물에 던져도 오리는 수영하지만 전기 오리는 수영을 할수가없다. 

부모 클래스에서 사용되는 모든 메서드를 재정의 해야된다면 이거는 뭔가 잘못됬다는거다.  하위타입인 전기 오리와 오리가 뒤바뀐다면 문제가 생길거다. 

![Untitled 15](https://user-images.githubusercontent.com/72185011/171400904-49288792-a08c-44a8-82cf-2c75a584d8aa.png)


클라이언트가 사용하지 않는 메소드에 강제로 의존해서는 안된다.

![Untitled 16](https://user-images.githubusercontent.com/72185011/171400966-dd947526-a160-4984-bc33-d58e43900d93.png)


위가 적절한 인터페이스 분리 원칙의 짤이다. 

특히 자바 경우에 더 큰 문제를 일으키는데 예를 들어서 클래스에서 하나가 변경되면 다음 모두에게 영향을 미치는 컴파일 언어기 때문이다.
![Untitled 17](https://user-images.githubusercontent.com/72185011/171400947-dc5ef694-14b8-46f8-a348-1ae6dbe0c1d7.png)

![Untitled 18](https://user-images.githubusercontent.com/72185011/171400954-1cf2ad54-1192-4594-89ef-4b8c0161d42c.png)


기본적으로 해야 할 일은 큰 인터페이스를 쪼개서 대체하는 방법이다.

Subscriber 인터페이스로 여러가지의 메소드를 받고있는걸 쪼개보자.

![Untitled 19](https://user-images.githubusercontent.com/72185011/171400984-e48f1210-180a-4ddb-82ba-e2c5a3e13a41.png)


이메일 알림만 존재하는 인터페이스를 받아 큰 덩어리인 Subscriber에서 분리하였다. 

![Untitled 20](https://user-images.githubusercontent.com/72185011/171400997-c5137440-9c92-415a-826c-8102af56baed.png)


- 구체적인것에 의존하지않고 추상적인것에 의존한다

추상적인것 이게 포인트다.

![Untitled 21](https://user-images.githubusercontent.com/72185011/171401010-471a46f8-ee22-422b-bd81-561645060115.png)


전기를 얻어야될때 소켓 꼭다리에 그냥 플러그를 꼽지 벽에 구멍 뚫어가지고 배선 찾아서 전기 연결하지는 않는다.

![Untitled 22](https://user-images.githubusercontent.com/72185011/171401022-975c7973-7448-45c3-b495-2574d8f998b6.png)

![Untitled 23](https://user-images.githubusercontent.com/72185011/171401033-999637c6-7675-4c86-b6bb-4b4d5e71327f.png)

index 메소드의 User에는 구체적인 사용자 리파지토리가 존재한다. 

MongoDB에서 작동하는 클래스와 파일들이 구현되어 있다면 다른 DB로

변경할 시 이때까지 배웠던 SOLID에 위반되고 구체적인것에 의존하면 안된다는 DIP또한 지킬수없다. 인터페이스는 오직 인터페이스에만 의존해야한다.

![Untitled 24](https://user-images.githubusercontent.com/72185011/171401047-9dd40d8e-c655-4799-bf6c-7a01ba5e6782.png)

SOLID 강박에서 벗어나라 

![Untitled 25](https://user-images.githubusercontent.com/72185011/171401061-2f20c113-29d1-493b-a7f3-4038eb4fa136.png)


솔리드 디자인 원칙은 원칙일뿐이지, 규칙이 아니다 SOLID라는 틀에 맞추려하지말고 유지보수성을 위해 SOLID를 사용하려고 해보자. 

![Untitled 26](https://user-images.githubusercontent.com/72185011/171401082-d0aaac06-6355-431c-8d76-ab67bf62a8be.png)


스택 오버 플로우에 올라온 예시이다 어떻게 코드 조각화를 피할수있을까? 팀 리더가 SOLID 광신도여서 광적인 SRP 리펙토링을 지키려해서 인터페이스 파라미터가 20개씩 쪼개지고 천년의 퍼즐마냥 조각화나버렸다.

소프트웨어의 유지보수성이야 말로 스프트웨어의 확장성이다. 누가 봐도 이해하기 쉬운게 좋은 코드다. SOLID는 이런 유지보수성을 위해 나온 원칙이지만 광적으로 여기에 틀을 맞추려다 더 유지하고 확장하기 어렵다고 느껴진다면 그건 뭔가 잘못된거다.

![Untitled 27](https://user-images.githubusercontent.com/72185011/171401091-404054d9-3860-428b-9390-fe9496af9100.png)

![Untitled 28](https://user-images.githubusercontent.com/72185011/171401099-4aed6659-6b55-460b-a253-7a1acb117e94.png)


카테리나의 정리는 이러하다. 더 좋은 코드와 구조를 위해 SOLID에 맞게 사용하는건 좋다. 어디까지나 SOLID는 TOOL이지 GOAL 아니다. 코드를 작성하는데 더 많은 시간이 걸릴거다 대신에 코드를 쉽게 이해할수있어서 읽는 속도가 엄청 줄어들거다.

 따봉순 댓글

- 사진
    
    
    ![Untitled 29](https://user-images.githubusercontent.com/72185011/171401127-cd1e65ad-d7f2-426c-b9f2-95175f16cc12.png)


> 리스코프 치환 원칙은 소수만이 명확하게 이해할 수 있는 가장 간단한 원리다. 이게 잘 되있으면 당신 설계가 얼마나 추상화 관점에서  잘 설계되어있는지 나타낸다. 예를들어 동물 클래스 경우 모든 동물은 날거나 수영할 수 없기때문에 “이동” 이라는 메소드를 사용하는게 옳다. 추상적으로 참인 것들만 존재해야한다.
> 

> "SOLID를 달성하려고 하지 마십시오. SOLID를 사용하여 유지보수성을 확보하십시오." - 아 좋습니다,,
> 

> 이론적으로 이야기하는거만 너무 많이봤는데 코드 예제가 있어서 너무 도움이 됩니다.
> 

> 대규모 엔터프라이즈급 프로젝트에서 백엔드 개발자로 일했는데 너무 많은 클래스가 있었고 전체 코드는 SOLID를 따르려고 노력했지만 결국 스파게티가 되었습니다. 모든 것이 종교처럼 추상화 되어서 새로운 사람이 정말 생산적으로 일하기까지 몇 주가 걸렸는데 이 내용이 이번 강연에 나오게되서 기쁩니다. 우효!
> 

> 이 원칙들을 명확하게 설명해줘서 고맙고 많은 사람들이 진리로 받아들이는 생각을 이렇게 단점도 있다라는걸 말하기는 이 분야에서 정말 힘든일이다.  다 지키는 이상적인 상황은 꿈의 세계에서는 가능하겠지 아마도…
>
