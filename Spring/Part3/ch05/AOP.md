# Spring
## AOP

대부분 MVC 웹 어플리케이션을 사용. 제일 앞에 웹 레이어, 가운데는 서비스라고 불리는 비즈니스 레이어, 마지막에 데이터 레이어가 있음

* Web Layer : REST API를 제공. Client 중심의 로직을 적용
* Business Layer : 내부 정책에 따른 logic 개발. 주로 개발하는 영역
* Data Layer : 데이터베이스와 및 외부와의 연동

로그를 남기거나, 시간을 체크하는 등의 부가적인 기능이 사용될 수 있음
요런 것들을 한곳으로 몰아서 편하게 쓸수 있도록 해주는 거

**AOP의 주요 어노테이션**
* `@Aspect` : AOP를 정의하는 class에 달아줌
* `@Pointcut` : 기능을 어디 적용시킬지. 메소드? 어노테이션? 등 AOP를 적용 시킬 시점을 설정
* `@Before` : 메소드 실행하기 이전
* `@After` : 메소드 실행 이후
* `@AfterReturning` : 메소드 호출 성공시
* `@AfterThrowing` : 메소드 호출 실패 exception 발생 시
* `@Around` : Before / After 둘다





### AOP 사용하기

* AOP를 사용하려면 의존성 추가를 하셔야되용~

프로젝트 내 `build.gradle` -> `dependencies`에서     
`implementation 'org.springframework.boot:spring-boot-starter-aop'` 추가. 걍 밑에거 복사해서 aop만 바꿈 됨ㅋ 새로고침 하시고요    

지금까지 만든 일반적인 controller에서는 일일이 다 `println()`을 해서 봐야 했음
```java
@GetMapping("/get/{id}")
    public void get(@RequestParam String name, @PathVariable Long id){
        System.out.println("get method");
        System.out.println("id : " + id);
        System.out.println("name : " + name);
```

이렇게

근데 이거 너무 귀찮음. 그래서 aop를 사용

* **AOP를 사용하는 클래스 만들기**
```JAVA
@Aspect //aop를 사용하는 클래스
@Component  //spring이 관리하는 클래스
public class ParameterAop {
```

클래스를 선언. 이때 어노테이션 두개를 선언
* `@Aspect` 어노테이션. aop를 사용하는 클래스라는 뜻.
* `@Component` 어노테이션. `spring container`에서 `Bean`으로 관리하는 클래스라는 뜻     

* 다음으로는 **`@Pointcut`** 을 선언하셔야 되용

`@Pointcut("execution(* com.example.aop.restApiController..*.*(..))")`

아래 메소드가 pointcut 메소드임을 선언하는 어노테이션입니다.     
포인트컷에는 `execution`을 설정해 주셔야 되는데요, 무려 수식언은 걍 알아서 인터넷 뒤지래요^^     

`* com.example.aop.restApiController..*.*(..)`요거는 해당 경로 하위에 있는 모든 메소드를 aop로 보겠다~ 라는 뜻이라네요^^
이 밑에는 ` private void cut(){}`걍 이렇게 간단히 메소드 선언하심 되구요


* 다음으로는 실제로 pointcut을 사용하는 메소드들을 만들어줄 거에요~

* **`@Before`** 어노테이션     

```@Before("cut()")    //아래 메소드를 cut()메소드 실행 이전에 실행하겠다.
    public void before(JoinPoint joinPoint){    //들어가는 지점에 대한 정보를 갖는 joinPoint 인자
        Object args[] = joinPoint.getArgs();    //메소드에 들어가고 있는 인자들을 받아볼 수 있음

        for(Object obj : args){
            System.out.println("type : " + obj.getClass().getSimpleName());
        }
    }
```

`@Before`어노테이션은 특정 메소드 실행 전에 이 메소드를 실행하겠다!라는 뜻이에용     
인자로는 메소드명을 문자열로 주면 됩니다! 그럼 그 인자로 받은 메소드 전에 실행되요    

` public void before(JoinPoint joinPoint){`

메소드 내용입니다. JoinPoint는 들어가는 지점, 그니까 여기서는 cut()메소드에 대한 정보를 갖고있는 객체에요.

`joinPoint.getArgs();`를 통해서 cut()에 들어가는 인자들을 볼 수 있어요.

```java
for(Object obj : args){
            System.out.println("type : " + obj.getClass().getSimpleName());
            System.out.println("value : " + obj);
        }
```


* **`@AfterReturning`** 어노테이션

`@AfterReturning` 어노테이션은 특정 메소드가 성공적으로 실행 된 후 리턴까지 한 후에 실행하겠다 는 어노테이션

```java
 @AfterReturning(value = "cut()", returning = "obj")    //아래 메소드를 cut()메소드가 성공적으로 호출되고 리턴한 후에 실행하겠다.
                                    //받을 obj를 이름을 맞춰서 returning으로 받아줘야 함.
 public void Return(JoinPoint joinPoint,Object obj){
```

인자로는 `value`와 `returning`을 줄수 있는데, value는 똑같이 실행 기준이 되는 메소드명이고, returning은 해당 메소드의 리턴값을 받아올 수 있음.    
메소드 인자로는 `JoinPoint`와 `Object`가 있는데, `Object`인자의 객체명과 returning을 맞춰줘야 함.


* **재밌는 어노테이션 만들기~**

직접 어노테이션을 만들 수 있습니당.

* 클래스 생성에서 어노테이션을 생성

```java
@Target({ElementType.ANNOTATION_TYPE,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Timer {
```

* `@Target` : 이 어노테이션이 어디에 달릴지 정하는 어노테이션입니다.     
  -`ANNOTATION_TYPE`은 클래스,인터페이스,enum에 이 어노테이션을 달 수 있다는 얘기고
  -`METHOD`는 메소드에 이 어노테이션을 달 수 있다는 말입니다.
* '@Retention` : 해당 어노테이션이 언제까지 살아있는지를 의미합니다. RUNTIME으로 주면 그냥 계속 살아있습니다.

`@Timer` 어노테이션을 선언했으면 `TimerApp` 클래스를 생성해줍니다.

```java
@Aspect
@Component  //@Bean은 클래스에는 못붙인다고 하네요~
public class TimerAop {
    @Pointcut("execution(* com.example.aop.restApiController..*.*(..))")
                            //controller 하위의 메소드들에 건 pointcut
    private void cut(){}

    @Pointcut("@annotation(com.example.aop.annotation.Timer)")
                            //@Timer 어노테이션이 설정된 메소드들에 사용할 pointcut
    private void enableTimer(){}

    @Around("cut() && enableTimer()")
    public void around(ProceedingJoinPoint joinPoint) throws Throwable {

        StopWatch stopWatch = new StopWatch();
        stopWatch.start();

        Object result = joinPoint.proceed();
        //joinPoint의 proceed를 호출하면 실제 메소드가 실행됨. return값이 있다면 Object 타입이 리턴됨
    }
}
```

`TimerApp`에는 두개의 `pointcut`을 붙여줍니다.

* 첫번째는 전에 붙였던 `execution` pointcut입니다.     
`* com.example.aop.restApiController..*.*(..))"`
    
    controller 하위 메소드들에게 적용한다는 경로를 지정하는 pointcut입니다.    
    `cut()`메소드에 달아줍니다.

**앞으로 `cut()`을 인자로 전달받은 메소드는 `restApiController`의 하위 메소드들이 실행될때마다 자동으로 실행됩니다.**


* 두번째는 어노테이션 pointcut입니다.
  `@Pointcut("@annotation(com.example.aop.annotation.Timer)")`
  
  `@Timer` 어노테이션이 적용된 메소드들에게 적용한다는 pointcut입니다.    
  `enableTimer()` 메소드에 달아줍니다.
  
**앞으로 `enableTimer()`를 인자로 전달받은 메소드는 `@Timer` 어노테이션이 적용된 모든 메소드가 실행될때마다 자동으로 실행됩니다.**

* 다음으로 실제로 실행할 메소드에 대해 작성합니다.

```java
@Around("cut() && enableTimer()")
    public void around(ProceedingJoinPoint joinPoint) throws Throwable {
```

`around` 메소드를 정의합니다. 우선 `@Around` 어노테이션을 달아줍니다.    
`@Around` 어노테이션은 인자로 전달받은 pointcut에 적용되는 메소드에 대해, 메소드가 시작할때와 끝날때 아래 메소드(`around()`)를 적용합니다.

이어서 around()는 인자로 ProceedJoinPoint를 받습니다. 

`Object result = joinPoint.proceed();` 
메소드 내부에는 joinPoint객체에 대해 proceed()를 실행합니다. proceed()는 실제로 해당 메소드를 실행합니다.    
joinPoint로 받는 메소드가 리턴값이 있다면 result에 저장합니다.

```java
StopWatch stopWatch = new StopWatch();
        stopWatch.start();

        Object result = joinPoint.proceed();
        //joinPoint의 proceed를 호출하면 실제 메소드가 실행됨. return값이 있다면 Object 타입이 리턴됨

        stopWatch.stop();

        System.out.println("total time : " + stopWatch.getTotalTimeSeconds());
        //특정 메소드를 시작하고부터 끝날때까지 걸린 시간을 출력하는 메소드
```

위 proceed()메소드를 

```java
StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        
        //proceed()
        
        stopWatch.stop();
```
로 감쌉니다. 이렇게 해당 메소드가 실행되는 시간을 측정합니다.

`System.out.println("total time : " + stopWatch.getTotalTimeSeconds());`

그리고는 실행 시간을 출력합니다.


잘 해봤죠? Decode는 코드만 남겨드립니다~


### `DecodeApp`
```java
public class DecodeAop {

    @Pointcut("execution(* com.example.aop.restApiController..*.*(..))")
    private void cut(){}

    @Pointcut("@annotation(com.example.aop.annotation.Decode)")     //@Decode 어노테이션 달린 메소드에 적용
    private void enableDecode(){}

    //전은 decode를 해서 들여오고, 후는 incode를 해서 내보낼거임

    @Before("cut() && enableDecode()")
    //전. decode 과정
    public void before(JoinPoint joinPoint) throws UnsupportedEncodingException {

        Object args[] = joinPoint.getArgs();
        //joinPoint를 통해 가져온 메소드 인자값을 Object args[] 배열로 담아옴

        for(Object arg : args) {    //받아온 배열에 대해
            if (arg instanceof User) {     //if(Object로 받아온 arg의 실제 타입이 User 이면)

                //User user = User.class.cast(arg);
                User user = (User) arg; //object에서 User 타입으로 형변환 후

                String base64Email = user.getEmail();   //메일 주소만 string으로 꺼내옴
                String email = new String(Base64.getDecoder().decode(base64Email),"UTF-8");
                                //메일 주소를 base64로 디코딩 수행, UTF-8로

                user.setEmail(email);   //디코딩 한 메일주소를 user에 다시 set해줌
            }
        }
    }

    @AfterReturning(value = "cut() && enableDecode()", returning = "returnObj")
    public void afterReturn(JoinPoint joinPoint,Object returnObj){

        if(returnObj instanceof User){

            User user = (User) returnObj;

            String email = user.getEmail();

            String base64Email = new String(Base64.getEncoder().encodeToString(email.getBytes(StandardCharsets.UTF_8)));

            user.setEmail(base64Email);
        }

    }
}
```

**Decode 메서드 부분**
```java
@Decode
    @PutMapping("/put")
    public User put(@RequestBody User user){

        System.out.println("put");
        System.out.println(user);

        return user;

    }
```
