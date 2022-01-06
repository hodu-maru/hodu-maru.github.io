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

받은 request,response 객체를 `HttpServletRequest`, `HttpServletResponse`로 형변환해줍니다.
그 다음 해당 객체들을 통해 request에 담긴 정보들을 얻을 수 있습니다.

`String url = httpServletRequest.getRequestURI();`  : request의 URI를 얻을 수 있음

`BufferedReader br = httpServletRequest.getReader();` //버퍼를 통해 body내용을 받아옴

```java
 br.lines().forEach(line -> {
            log.info("url : {}, line : {}",url,line);
        });
        //버퍼로 받은 body를 한줄씩 log로 남김
```

근데 이렇게만 만들고 돌리면 에러가 뜹니다. 왜??

`getReader() has already been called for this request`

한번 buffer를 통해 request로 들어오는 body를 읽으면, 커서가 body의 끝 부분에 이기 가버려서 한번 읽은 url body를 다시 읽을 수 없음.

이를 해결하기 위해, 캐싱을 사용함.

**Caching이란?**

캐싱 기본 개념 : 캐싱(Caching)은 애플리케이션의 처리 속도를 높여준다. 이미 가져온 데이터나 계산된 결과값의 복사본을 저장함으로써 처리 속도를 향상시키며, 이를 통해 향후 요청을 더 빠르게 처리할 수 있다. 대부분의 프로그램이 동일한 데이터나 명령어에 반복해서 엑세스하기 때문에 캐싱은 효율적인 아키텍처 패턴이다.

웹 캐시(WEB Cache) :  사용자(client)가 웹 사이트(server)에 접속할 때, 정적 컨텐츠(이미지, JS, CSS 등)를 특정 위치(client, network 등)에 저장하여, 웹 사이트 서버에 해당 컨텐츠를 매번 요청하여 받는것이 아니라, 특정 위치에서 불러옴으로써 사이트 응답시간을 줄이고, 서버 트래픽 감소 효과를 볼 수 있다

```java
        ContentCachingRequestWrapper httpServletRequest = (ContentCachingRequestWrapper) request;
        ContentCachingResponseWrapper httpServletResponse = (ContentCachingResponseWrapper) response;
```

`ContentCaching`을 사용해서 request와 response를 만들면, 저장해둔 것을 사용해 계속 사용할 수 있다.

이렇게 caching을 사용해서 바꾼 뒤 다시 프로그램을 돌리면,

`ClassCastException` 다시 에러가 남.

야발 이새끼 의도한거 아닌것같은데.

```java
        ContentCachingRequestWrapper httpServletRequest = new ContentCachingRequestWrapper ((HttpServletRequest)request);
        ContentCachingResponseWrapper httpServletResponse = new ContentCachingResponseWrapper((HttpServletResponse)response);
```

ContentCaching을 사용할 때는 HttpServlet으로 형변환을 시킨 뒤, 해당 객체를 ContentCaching클래스에 인자로 줘 new로 생성해서 사용하셔야 된다고 합니다.


**진짜 개 ㅈ같은 설명 어휴**

**1.ContentCaching으로 request와 response를 생성한다.**

```java
        ContentCachingRequestWrapper httpServletRequest = new ContentCachingRequestWrapper ((HttpServletRequest)request);
        ContentCachingResponseWrapper httpServletResponse = new ContentCachingResponseWrapper((HttpServletResponse)response);
```

이후 `doFilter()`를 거친 이후에


```java
        String url = httpServletRequest.getRequestURI();    //어떤 주소를 요청했는지 확인가능
        String contect = new String(httpServletRequest.getContentAsByteArray());
        //content의 내용을 byte로 받아 string으로 저장

        //ContentCachingRequestWrapper에서, 생성자에서는 content의 길이만 지정해 놓고, 내용은 나중에 사용을 한대
        //spring에서 모두 mapping한 다음 사용..?

        String requestContent = new String(httpServletRequest.getContentAsByteArray());
        //content내용을 byteArray로 받음

        String responseContent = new String(httpServletResponse.getContentAsByteArray());
        int httpStatusCode = httpServletResponse.getStatus();
```

**2.request로부터 정보를 받아옴**

  `String url = httpServletRequest.getRequestURI();`
  
  url정보를 받아오는 메소드
  
  `String contect = new String(httpServletRequest.getContentAsByteArray());`
  
  request의 body를 byte로 받아온 다음 string타입으로 변환해 저장함
  
 `String responseContent = new String(httpServletResponse.getContentAsByteArray());`
 
 response도 받아올 수 있음
 
 **3.copyBody로 한번 읽은 body를 계속 사용할 수 있게 함**
 
 `httpServletResponse.copyBodyToResponse();`
 
 요걸 해줘야 다시 body를 읽고 사용할 수 있음
 
 
 `log.info("response status : {}, responseBody : {}",httpStatusCode,responseContent);`
 
 이후에 log.info()로 정해진 형식의 log를 남길 수 있음
 
 **이렇게 만든 filter를 특정 부분에 적용하는 방법**
 
 * ApiController에 filter를 적용하는 방법

1. Filter에다가 `@WebFilter` 어노테이션을 달고, `@Component`는 지워준다.

2. 어노테이션에 `urlPatterns = `인자로 filter를 지정할 url을 지정해준다.

`@WebFilter(urlPatterns = "/api/user/*") //하고자 하는 url 설정`

굳~



 
 
 


  
  














