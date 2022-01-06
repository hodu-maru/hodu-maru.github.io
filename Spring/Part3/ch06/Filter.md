# Spring
## Filter

### Filter란?

* Intercepter와 유사하지만, Spring Context에 등록된다는 차이가 있다.
* 주로 **인증단계**를 처리하고나, Logging을 하는데에 사용된다.
* 이를 선,후 처리함으로써 Service business logic과 분리시킨다.


### Filter 사용법

* 우선 접근권한이 다른 두 controller를 만든다.

```java
@RequestMapping("/private")
public class PrivateController {

    @GetMapping("/hello")
    public String hello(){
        return "private hello";
```

```java
@RequestMapping("/public")
public class PublicController {


    @GetMapping("hello")
    public String hello(){
        return "public hello";
```

두 클래스는 공용과 private 이므로, 접근권한이 구분되어야 한다.

어노테이션으로 이를 만들어 보자.

```java
@Documented //해당 어노테이션을 사용하는 클래스가 javadoc과 같은 문서화 될 때,해당 어노테이션이 적용되었음을 명시하도록 한다.라고 하네요
@Retention(RetentionPolicy.RUNTIME) //이 어노테이션이 얼마나 오래 살아있을지. RUNTIME이면 걍 계속 살아있음
@Target({ElementType.TYPE,ElementType.METHOD})  //어노테이션의 사용 범위. 모든 클래스, 인터페이스, 어노테이션들과 메소드에 붙일 수 있음
public @interface Auth {
```

짜잔. 어노테이션 만드시는 법, 기억하시나요? 일단 어노테이션 3개를 달아주세요.
`@Documented` : 몰라요. 걍 다세요.
`@Retention` :  이 어노테이션이 얼마나 오래 살아있을지래요. RUNTIME 박으세요.
`@Target` : 어노테이션의 사용 범위에요. 걍 두루두루 쓰고 싶으면, TYPE이랑 METHOD 박으세요.

