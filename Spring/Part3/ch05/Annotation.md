# Spring
## Spring Boot Annotations

* `@SpringBootApplication` : Springboot Application으로 설정함
* `@Controller` : view를 제공하는 controller로 설정
* `@RestController` : rest api를 제공하는 controller로 설정
* `@RequestMapping` : URL주소를 맵핑
* `@GetMapping` : get으로 받는 url주소 매핑
* `@PostMapping` : Post로 받는 url주소 매핑
* `@PutMappting` : Put으로 받는 url주소 매핑
* `@DeleteMapping` : Delete로 받는 url주소 매핑
* `@RequsetParam` : URL QueryParameter 매핑
* `@RequestBody` : Body를 parsing매핑
* `@Configration` : 1개 이상의 bean등록시 
* `@Component` : 클래스 전체를 bean등록시 사용
* `@Bean` : 메소드를 bean으로 등록시 사용
* `@Qualifier` : 두개 이상의 bean이 있을 때 명시적으로 사용시
* `@Aspect` : AOP 적용시 사용
* `@Before` : aop메소드 이전에 호출
* `@After` : AOP메소드 이후에 호출. exception발생해도 호출됨
* `@Around` : aop메소드 이전/이후 모두 exception 발생해도 호출
* `@AfterReturning` : aop메소드 호출이 정상적으로 되었을 때 호출
* `@AfterThrowing` : aop메소드 호출이 exception이 발생했을 때 호출

