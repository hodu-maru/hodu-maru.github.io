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

* `@Slf4j` : Controller에서 `System.out.println` 대신 log를 사용할 수 있게 하는 어노테이션
