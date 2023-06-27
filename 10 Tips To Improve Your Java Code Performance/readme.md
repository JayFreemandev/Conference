1. **String Declaration**

String a = “Apple” o

String a = new String(”Apple”); x

Java에서 String은 참조 자료형이다. String 객체는 자바 내장 클래스로 위와 같이 new 키워드로 새로운 객체를 생성할 수도 있고, " "안에 값을 입력하여 생성할 수도 있다.

두 가지 방식 모두 String 객체를 생성한다는 사실은 같지만, JVM이 관리하는 메모리 구조상에서 다르다.

![[http://www.journaldev.com/797/what-is-java-string-pool](http://www.journaldev.com/797/what-is-java-string-pool)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0e179d11-6658-44bd-a3ed-3b9b947f8b35/Untitled.png)

[http://www.journaldev.com/797/what-is-java-string-pool](http://www.journaldev.com/797/what-is-java-string-pool)

new 생성자를 이용해서 인스턴스를 생성한 뒤, heap에서 메모리 관리가 이루어 진다는 사실은 다른 참조 자료형과 다를게 없다. 하지만 다른 참조형과는 다르게 변하지 않는다는 특징을 가지고 있다.  ****String은 **불변**이다.

한번 저장된 String 객체의 값은 변하지 않는다. 위의 그림에서 s3 = s3 + s3; 를 실행한다면 CatCat값을 가지는 String 객체를 새로 생성하고, s3는 생성된 인스턴스를 참조하게 된다.

결과적으로 String 객체들의 연산이 이루어지면, 새로운 객체를 계속 만들어내기 때문에 메모리 관리 측면에서 비효율적이다.

이러한 이유로 만들어진 메모리 영역이 Heap 안에 있는 String Constant Pool이다. 여기에는 기존에 만들어진 문자열 값이 저장되어 있고, s1과 s2처럼 리터럴로 생성된 같은 값을 가지는 객체는 같은 레퍼런스를 가지게 된다.

1. **Empty String Comparaision**

str.equals(””)  

str.length == 

문자열을 비교하기 위해서는 length, empty, isempty와 같은 메소드들이 존재한다 단순 성능만으로 본다면 ==이 우세하다 정말 길이만 체크하기 때문이다.

equals는 단순 길이뿐 아니라 문자열의 내용이 같은지까지 비교하게된다. 길이를 리턴하는 것보다는 복잡한 로직이 들어간다.

isempty경우는 ==와 동일하게 길이만을 판단하고 0인 boolean타입이기에 0이면 true 아니면 false인데 int를 리턴하느냐 boolean을 리턴하느냐가 length와의 차이점이다. 

위 두개의 메소드 경우는 null체크가 없기때문에 한 번 더 확인이 필요하다. 200만개의 배열에서 길이가 0에서 7인 문자열을 위 세가지로 비교했을때의 속도는 0.01에서 0.02의 속도 차이를 보인다.

값이 더 커진다면 성능의 10%, 20%의 차이를 가져온다. null 체크가 필요없는 경우는 가독성을 위해서라도 length보다는 isempty가 좋을거같다.

1. Avoid Boxing and Unboxing

Try to use primitive types whenever possible 

public Integer → public int  o

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/92bd0458-1b53-4d75-a6e6-befd10d23137/Untitled.png)

```java
class Main {
    public static void main(String[] args) {
        // 1. auto Boxing
        Integer num1 = 20;

        // 2. auto Unboxing
        int num2 = num1;
    }
}
```

리턴타입말고도 jdk 1.5부터는 auto boxing unboxing이 제공되는데 속도 좀 느려도 null받을 수있어서 좋기는 하지만 이펙티브 자바에서도 가능하면 원시타입 사용하고 collection떄문에 참조타입만 오케이.

1. 같은 값을 가진 두개의 인스턴스를 == 로 비교하면 주소값때문에 다르게 판단됨 이때 언박싱하는게 하나의 cost 
2. 참조타입과 원시타입 비교시 위와같이 언박싱 되는데 null 참조를 언박싱한다면 NPE 터짐
3. 계산식에서 사용될 경우 1억번 반복시 10배 이상의 성능 차이
4. 자동 언박싱하면서 객체 생성되는데 가비지 컬렉터에  일거리를 늘게해서 STW 자주 발생

1. Underscore between Numbers

int value = 123_123;

long value = 123_123L;

float value =123_123; 

String value = “123_123”;

Integer.parseInt(val); xxxx

위와 동일(코스트)

1. ArrayList vs Vectors

Vector는 레거시 컬렉션, 동기화가 되어있어 thread per request고  안전하나 ArrayList에 비해 느리다. 동적 배열로 크기 증가시 ArrayList와 비교해서 100% 증가해버린다. 

1. Boolean

boolean isTrue = false;

if(uset.getName().equals(token.getName()){

isTrue = true;

}

보다는 boolean isTrue =uset.getName().equals(token.getName()를 사용하라는건데 이거는 성능의 차이보다는 가독성의 차이라고 생각한다.

1. Logging Using Lamda

```java
//Method1
Logger logger = Logger.getLogger(currentClass);
logger.log(Level.INFO,"info log);

//Method2
Logger logger = Logger.getLogger(currentClass);
logger.log(Level.INFO,() -> "info log);
```

1. Avoid Unnecessary Mthod Calls Repetition

```java
for(int i=0; i<item.lengh();i++) x

int size = item.length();
for(int x=0; x<size; i++) o
```

1. try catch finally → try with resource

1. use stream

기준이 너무 모호

**conclusion** 

5,6,7,10은 이해불가

https://www.youtube.com/watch?v=uEHJ5CHaF08

Refactoring Java Code: How to refactor code to write cleaner and more maintainable code.
https://www.youtube.com/watch?v=u4Dc_Pzl-50&t=792s

Refactoring Code for Cleaner Code: Decompose conditional logic and extract function
https://www.youtube.com/watch?v=T3iTI2uEwkc&list=PLQ07BhiT-NEm5LfLO47rDLoFF_FmI2g71&index=3

Victor Rentea
https://www.youtube.com/watch?v=F02LKnWJWF4&t=312s

Victor 리팩토링 영상
https://www.youtube.com/watch?v=iOYsxBvMkLk&t=2489s

optional
https://www.youtube.com/watch?v=vKVzRbsMnTQ



**reference**

[https://www.youtube.com/watch?v=frhNwZo_eQE](https://www.youtube.com/watch?v=frhNwZo_eQE)

[https://ict-nroo.tistory.com/18](https://ict-nroo.tistory.com/18)
