# Spring
## Exception처리


* Exception 처리

web application의 입장에서, 에러가 났을 때 알려줄 방법은 많지 않음

**처리 방법 **

1.`@ControllerAdvice`

2.`@ExceptionHandler`

**그전에 validation 복습!**

1.build.gralde에서 validation 의존성 추가

`implementation 'org.springframework.boot:spring-boot-starter-validation'`

2.dto에 valid 어노테이션 달기

```java
@NotEmpty
    @Size(min = 1, max = 10)
    private String name;

    @Min(1)
    @NotNull
    private Integer age;
```

* `name`은 empty여선 안되고, 최소 1, 최대 10의 값을 갖는다.
* `age`는 최소 1이고, null이여서는 안된다.

3.validation이 잘 적용되는지 확인하기

```java
@GetMapping("") // ?name=1234
    public User get(@RequestParam(required = false) String name, @RequestParam(required = false) Integer age) {
        User user = new User();
        user.setName(name);
        user.setAge(age);

        int a = 10 + age;

        return user;
    }
```

`required = false` 는 인자가 들어오지 않아도 에러 없이 동작하도록 함

값이 안들어와도 메소드가 돌긴 함. 근데 setAge(age)에서 nullPointerException이 발생할 거임

근데 이런 에러들에 대해서, 기본적으로 spring은 자세한 설명을 해주진 않음. 그래서 다음과 같은 방법들을 사용함


### Controller Advice를 사용하는 방법

에러를 다루는 방법~

```java
@RestControllerAdvice   //rest API를 사용하는 controller
public class GlobalControllerAdvice {

    @ExceptionHandler(value = Exception.class) //예외 처리 어노테이션. 값으로 어떤 에러를 잡을지 설정. 얘는 걍 모든 예외 처리
    public ResponseEntity exception(Exception e){
```

api 에러 관련 처리를 할거기 때문에 `@RestControllerAdvice` 어노테이션을 단 클래스 선언.

이후 내부에 메소드 선언. API 처리 결과를 표시할 거기 때문에 `ResponseEntity`를 반환형으로 지정.

메소드에는 `@ExceptionHandler`이라는 예외 처리 어노테이션을 선언. 해당 어노테이션 인자로 어떤 에러를 잡을지 설정 가능.

`Exception.class`은 모든 예외를 처리한다는 뜻

메소드 인자로 `Exception` 타입을 받아 에러를 처리할 수 있음

```java
        System.out.println(e.getClass().getName());
        System.out.println(e.getLocalizedMessage());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
```

인자로 받은 예외타입 객체 e를 통해 에러에 대한 처리 가능. 

`e.getClass().getName()`을 통해 해당 에러가 어떤 형태의 에러인지 확인 가능

`e.getLocalizedMessage()`를 통해 에러 메세지 출력 가능

return으로 `ResponseEntity.status()`를 통해 해당 에러가 어떤 유형의 에러인지 지정 가능. 위 에러는 서버에서 발생한 에러이므로 INTERNAL_SERVER_ERROR 지정.

`body()`에서 구체적인 에러 메세지 작성 가능.

**구체적인 에러 유형 처리하기**

구체적인 에러 타입을 지정하면 특정 에러를 처리하는 메소드를 작성할 수 있음

에러 객체 e에 대해
`e.getClass().getName()`를 통해 해당 에러가 어떤 유형인지 클래스명으로 알아낼 수 있음

위 예시의 경우는
`org.springframework.web.bind.MethodArgumentNotValidException`임

```java
@ExceptionHandler(value = MethodArgumentNotValidException.class)
                        //해당 에러가 구체적으로 어떤 에러인지 특정할 수 있으면, @ExceptionHandler의 인자로 해당 에러 클래스를
                        //지정해 에러 처리 가능
    public ResponseEntity MethodArgumentNotValidException(MethodArgumentNotValidException e){
                                                            //구체적인 에러 타입으로 인자를 받음

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());

    }
```

에러 처리 메소드에 어노테이션으로 `@ExceptionHandler`를 달고, 그 인자로 처리하고자 하는 에러 타입을 클래스명으로 지정하면 특정 에러를 처리하는 메소드 작성 가능

`@ExceptionHandler(value = MethodArgumentNotValidException.class)`

위 어노테이션이 달린 메소드는 `MethodArgumentNotValidException`을 처리함

**@ControllerAdvice뿐만 아니라 그냥 RestController에서도 에러 처리 메소드를 작성할 수 있음 **

`@ControllerAdvice` 어노테이션을 단 클래스에서 작성한 에러 처리 메소드는 모든 영역에 대해 적용되지만,

`@Controller`나 `@RestController`를 단 실제로 동작하는 클래스 내부에서도 에러 메소드를 작성할 수 있음.

이때, 해당 클래스 내부에서 발생하는 에러는 그 클래스 내부에서 작성한 에러 처리 메소드에 의해 우선적으로 처리됨.











