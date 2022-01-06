# Spring
## SpringBootFilter와 Intercepter

**Filter**

web application에서 관리되는 영역으로, springboot framework에서 client로부터 오는 요청,응답에 대해  
최초,최종 단계의 위치에 존재하며, 이를 통해 요청,응답의 정보를 변경하거나 spring에 의해서 데이터가 변환되기 전  
순수한 client의 요청,응답 값을 확인할 수 있다. (뭐라는겨)

유일하게 ServeltRequest, ServeletResponse의 객체를 변환할 수 있다.

### lombok

* 자바 라이브러리
* 어노테이션으로 dto클래스의 getter(),setter(),constructor()등을 대신해줌

```java
//@Getter
//@Setter
@Data   //getter,setter,hashcode 등을 대신하는 어노테이션
@NoArgsConstructor  //getter, setter, 기본 생성자. -> 어노테이션만으로 클래스 내부에 따로 만들지 않아도 생성됨
@AllArgsConstructor //전체 인자를 받는 생성자
public class User {

    private String name;
    private int age;
}
```

* `@Getter` , `@Setter`  : getter와 setter를 대신 만들어주는 어노테이션
* `@Data` : getter,setter,hashcode 등을 대신 만들어주는 어노테이션
* `@NoArgsConstructor`, `@AllArgsConstructor` : 기본 생성자와 모든 인자를 받는 생성자를 대신 만들어주는 어노테이션


```java
@RestController
@RequestMapping("/api")
@Slf4j
public class ApiController {

    @PostMapping("/post")
    public User user(@RequestBody User user){
        log.info("User : {} , {}",user);
    }
}
```

* `@Slf4j` : Controller에서 `System.out.println` 대신 log를 사용할 수 있게 하는 어노테이션. lombok을 사용하면 달아주세요.

`        log.info("User : {} , {}",user,user);`

요렇게 사용하시면, 출력될 때 뒤 객체가 중괄호 안에 들어가는 형태로 찍히게 됩니다.

### Filter 만들기~

filter패키지를 만들고, 안에 `GlobalFilter`클래스를 생성할게요.

`public class GlobalFilter implements Filter `

Filter를 구현하는 GlobalFilter 클래스를 만들었어요. 이때, Filter는 **`javax.servlet`을 구현해 주셔야 합니다!!!!**

그다음 `doFilter`를 override해줄게요.

```java
        //전처리. 들어가기 전
        
        chain.doFilter(request,response);
        
        //후처리. doFilter가 돌면 response가 만들어짐
```
doFilter의 내부입니다. request와 response를 받구요, chain.doFilter()를 기준으로 전처리와 후처리가 나뉩니다.

![image](https://user-images.githubusercontent.com/75404119/148341668-d1c4ceab-e38f-408b-8c91-80a5fcf02294.png)

* WAS가 웹브라우져로부터 Servlet요청을 받으면 
1.요청을 받을 때 전달 받은 정보를 HttpServletRequest객체를 생성하여 저장

2.웹브라우져에게 응답을 돌려줄 HttpServletResponse객체를 생성(빈 객체)

3.생성된 HttpServletRequest(정보가 저장된)와 HttpServletResponse(비어 있는)를 Servlet에게 전달

* HttpServletRequest
1.Http프로토콜의 request 정보를 서블릿에게 전달하기 위한 목적으로 사용

2.Header정보, Parameter, Cookie, URI, URL 등의 정보를 읽어들이는 메소드를 가진 클래스

3.Body의 Stream을 읽어들이는 메소드를 가지고 있음

* HttpServletResponse
1.Servlet은 HttpServletResponse객체에 Content Type, 응답코드, 응답 메시지등을 담아서 전송함


```java
        HttpServletRequest httpServletRequest = (HttpServletRequest)request;
        HttpServletResponse httpServletResponse = (HttpServletResponse)response;  //응답용으로 생성해둔 빈 response객체
```

받은 request,response 객체를 `HttpServletRequest`,

