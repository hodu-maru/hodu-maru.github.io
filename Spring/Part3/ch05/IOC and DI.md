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


### DI 예시

①

` String url = "www.naver.com/books/it?page=105&size=20&name=spring"; //인코딩할 url`
  
위와 같은 URL을 Base64로 인코딩하는 작업을 수행해보자.

```java
public class Encoder {![146672810-0c755cdf-cc95-4f8b-9bf4-3a701079a31f](https://user-images.githubusercontent.com/75404119/146672817-cabb3958-e17f-426b-bfce-b686073550d4.png)

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


### IOC

**에휴시발 난 얘가 뭔 소리를 하는건지 당체 알수가 없다. 그래도 ㅈㄴ 중요한것같긴 하니까 적어야지,,,**

**IOC**. 개발자가 직접 new로 객체를 생성하지 않고, spring이 관리하는 클래스의 싱글톤 객체를 받아서 사용하는 것.

![image](https://user-images.githubusercontent.com/75404119/146672810-0c755cdf-cc95-4f8b-9bf4-3a701079a31f.png)

자 요롷게 `@SpringBootApplication` 어노테이션 옆에 있는 아이콘 누르면 현재 등록되어 있는 `Bean`들을 볼 수 있음.

빈은 어떻게 등록하냐,

```java
@Component
public class Encoder {
```

요렇게 @Component 어노테이션을 클래스에 선언하면 걔가 빈으로 등록됨.

그럼 이 빈을 어떻게 쓰냐,

```java
public class ApplicationContextProvider implements ApplicationContextAware {
    private static ApplicationContext context;
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
                                        //spring이 알아서 주입해줌
        context = applicationContext;
    }
    public static ApplicationContext getContext() {
        return context;
    }
}
```

요렇게 `ApplicationContextAware`를 구현하는 `ApplicationContextProvider` 클래스를 생성함. ~~이거 해야되는것부터 ㅈㄴ 귀찮~~

`ApplicationContext context = ApplicationContextProvider.getContext();`

메인 클래스로 돌아와서, 저렇게 context를 받아 주고,

`Encoder encoder = context.getBean("urlEncode",Encoder.class);`

요렇게 미리 만들어둔 Encoder 클래스 객체로 뭘 받냐, getBean메소드르 미리 지정해둔 bean을 받는거임. ㅅㅂ 솔직히 모르겠음.

빈 이름은 `@Component("urlEncode")` 요렇게 `@Component` 어노테이션의 인자로 전달할 수 있음.

```java
tring url = "www.naver.com/books/it?page=10&size=20&name=springBoot";
        String result = encoder.encode(url);
        System.out.println(result);
```

그 다음엔 걍 이렇게 씀 된대요.

솔직히 모르겠음 이새끼 설명 ㅈㄴ 못함
