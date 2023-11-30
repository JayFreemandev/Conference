Don Hash라는 유튜버가 정리한 10가지 자바 성능 향상에 관한 비디오의 내용이다. 성능상 관점도 있지만 가독성 관점에서 이야기한 부분도 있기 때문에 주의깊게 관찰 하여야한다.

1. **String Declaration**

```java
String a = “Apple” o

String a = new String(”Apple”); x
```

Java에서 String은 참조 자료형이다. String 객체는 자바 내장 클래스로 위와 같이 new 키워드로 새로운 객체를 생성할 수도 있고, " "안에 값을 입력하여 생성할 수도 있다.두 가지 방식 모두 String 객체를 생성한다는 사실은 같지만, JVM이 관리하는 메모리 구조상에서 다르다.

![image](https://github.com/JayFreemandev/Conference/assets/72185011/3d074858-84be-4c07-bc74-d3296b6b4fed)

http://www.journaldev.com/797/what-is-java-string-pool

new 생성자를 이용해서 인스턴스를 생성한 뒤, heap에서 메모리 관리가 이루어 진다는 사실은 다른 참조 자료형과 다를게 없다. 하지만 다른 참조형과는 다르게 변하지 않는다는 특징을 가지고 있다.  ****String은 **불변**이다.

한번 저장된 String 객체의 값은 변하지 않는다. 위의 그림에서 s3 = s3 + s3; 를 실행한다면 CatCat값을 가지는 String 객체를 새로 생성하고, s3는 생성된 인스턴스를 참조하게 된다.

결과적으로 String 객체들의 연산이 이루어지면, 새로운 객체를 계속 만들어내기 때문에 메모리 관리 측면에서 비효율적이다.

이러한 이유로 만들어진 메모리 영역이 Heap 안에 있는 String Constant Pool이다. 여기에는 기존에 만들어진 문자열 값이 저장되어 있고, s1과 s2처럼 리터럴로 생성된 같은 값을 가지는 객체는 같은 레퍼런스를 가지게 된다.

1. **Empty String Comparaision**

```java
str.equals(””)  

str.length ==
```

문자열을 비교하기 위해서는 length, empty, isempty와 같은 메소드들이 존재한다 단순 성능만으로 본다면 ==이 우세하다 정말 길이만 체크하기 때문이다.

equals는 단순 길이뿐 아니라 문자열의 내용이 같은지까지 비교하게된다. 길이를 리턴하는 것보다는 복잡한 로직이 들어간다.

isempty경우는 ==와 동일하게 길이만을 판단하고 0인 boolean타입이기에 0이면 true 아니면 false인데 int를 리턴하느냐 boolean을 리턴하느냐가 length와의 차이점이다. 

위 두개의 메소드 경우는 null체크가 없기때문에 한 번 더 확인이 필요하다. 200만개의 배열에서 길이가 0에서 7인 문자열을 위 세가지로 비교했을때의 속도는 0.01에서 0.02의 속도 차이를 보인다.

값이 더 커진다면 성능의 10%, 20%의 차이를 가져온다. null 체크가 필요없는 경우는 가독성을 위해서라도 length보다는 isempty가 좋을거같다.

1. **Avoid Boxing and Unboxing**

Try to use primitive types whenever possible 

public Integer → public int  o

![image](https://github.com/JayFreemandev/Conference/assets/72185011/a5866b65-1e46-49e7-84cc-77cc8a824d4f)


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

1. **Underscore between Numbers**

```java
int value = 123_123;
long value = 123_123L;
float value =123_123; 

String value = “123_123”;
Integer.parseInt(val); xxxx
위와 동일(코스트)
```

언더스코어를 사용(자바7)하면 숫자를 가독성 좋게 표현할 수 있으며, 큰 숫자의 경우 숫자 구분이 쉬워집니다. 예를 들어, 100만을 표현할 때는 1000000 대신 1_000_000과 같이 표현할 수 있습니다.

문자열 타입에서 저렇게 언더스코어를 쓸 경우 NumberFormatException이 발생한다. 성능상의 차이보다는 클린 코드의 관점에서 언더바는 문자열이 아니라 숫자를 구분하는데 사용하자.

1. **ArrayList vs Vectors**

Vector는 레거시 컬렉션, 동기화가 되어있어 thread per request고  안전하나 ArrayList에 비해 느리다. 동적 배열로 크기 증가시 ArrayList와 비교해서 100% 증가해버린다. 

1. **Boolean**

```java
boolean isTrue = false;

if(uset.getName().equals(token.getName()){
	isTrue = true;
}

boolean isTrue = user.getName().equals(token.getName(); O
```

이 부분도 성능 차이보다는 가독성 차이 인거같다.

1. **Logging Using Lamda**

```java
//Method1
Logger logger = Logger.getLogger(currentClass);
logger.log(Level.INFO,"info log");

//Method2
Logger logger = Logger.getLogger(currentClass);
logger.log(Level.INFO,() -> "info log");
```

위와 상동한다.

1. **Avoid Unnecessary Mthod Calls Repetition**

```java
for(int i=0; i<item.lengh();i++) x

int size = item.length();
for(int x=0; x<size; i++) o
```

Method1에서는 반복문이 실행될 때마다 **`item.length()`** 메소드가 호출되어 배열 또는 컬렉션의 길이를 다시 계산해야 합니다. 이는 반복문이 실행될 때마다 불필요한 메소드 호출이 발생하며, 메소드 호출에 따른 오버헤드가 발생할 수 있습니다.

Method2에서는 **`item.length()`**를 변수 **`size`**에 한 번만 호출하고, 반복문의 조건식에서는 **`size`** 변수를 사용합니다. 이렇게 하면 반복문 실행 도중 **`item.length()`** 메소드를 다시 호출할 필요가 없으므로, 불필요한 메소드 호출과 연산을 피할 수 있습니다.

1. **try catch finally → try with resource**

**`try-catch-finally`** 구문은 예외 처리와 관련된 리소스 관리를 수동으로 처리하는 반면, **`try-with-resources`** 구문은 자동으로 리소스를 관리하여 코드를 간결하게 유지할 수 있다.

try catch

```java
InputStream inputStream = null;
try {
    inputStream = new FileInputStream("file.txt");
    // 리소스 사용
} catch (IOException e) {
    // 예외 처리
} finally {
    if (inputStream != null) {
        try {
            inputStream.close();
        } catch (IOException e) {
            // 예외 처리
        }
    }
}
```

try with

```java
try (InputStream inputStream = new FileInputStream("file.txt")) {
    // 리소스 사용
} catch (IOException e) {
    // 예외 처리
}
```

1. use stream

기준이 너무 모호하여 생략…

**reference**

https://www.youtube.com/watch?v=frhNwZo_eQE

https://ict-nroo.tistory.com/18
