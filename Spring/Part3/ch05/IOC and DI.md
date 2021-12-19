# Spring
## Spring 시작하기 

### IOC
일반적으로 스프링에서 Java객체를 사용자가 **직접 new로 생성하는 일은 없다.** 대신, 객체를 Spring Container에게 모두 맡긴다.

즉, 개발자에게서 프레임워크로 **제어** 의 객체 관리 **권한이 넘어갔음** 으로 **제어의 역전** 이라 한다.

### DI
Spring container에게서 객체를 주입받아서 사용함


**DI의 장점**
* 의존성(특정 객체가 다른 객체에 의존함) 을 코드로부터 격리시킴 -> 특정 객체가 없더라고 코드를 돌리는데 문제 없음. 안정적으로 테스트가 가능
* 코드 변경시 영향을 최소화함(추상화)
* 순환 참조를 막을 수 있음


### IOC 예시

①

` String url = "www.naver.com/books/it?page=105&size=20&name=spring"; //인코딩할 url`
  
위와 같은 URL을 Base64로 인코딩하는 작업을 수행해보자.

```java
public class Encoder {
    //인코더 클래스 만들어 드렸습니다
    public String encode(String message){
        return Base64.getEncoder().encodeToString(message.getBytes());
```

일단 위와 같은 메세지를 받아서 base64로 인코딩해 반환하는 메소드를 만들었다.

``` Encoder encoder = new Encoder();
        String result = encoder.encode(url);
        System.out.println(result);
```

돌리면 잘 나온다.



아모르겠고 DI가 뭐냐 하면

```JAVA
Encoder DiEncoder = new Encoder(new Base64Encoder());
        //Encoder DiEncoder = new Encoder(new UrlEncoder());
        String DiResult = DiEncoder.encode(url);
        System.out.println(DiResult);
```

서로다른 인코더를 쓰고 싶을때, 인코더 클래스 자체를 생성하는게 아니고 인코더 클래스에다가 쓸 인코더 객체를 던져줘서 쓰는 방식
ㅇㅋ?
