# Spring
## Validation

* validation이란?
  java에서는 null값에 대해 접근시 null pointer exception이 발생함. 이러한 부분을 방지하기 위해 미리 검증하는 과정을 Validation이라고 함
  
  ex) 
  ```java
  public void run(String accout,String pw,int age){
    if(account == null || pw == null)
    return;
    
    if(age == 0)
    return;
    
    //정상 logic
    }
  ```   

null과 0을 걸러내고 있음. 실제 코드에서 다 이렇게 하면 너무 코드양이 많아짐
    
* **Validation 사용하기**

1. gradle dependecies (의존성 추가하기)
  `implementation("org.springframework.boot:spring-boot-starter-validation")`
  
2. bean validation spec  


* 지금까지의 일반적인 spring 코드는 controller와 dto로 구성.

문제없이 동작하지만, 옳지 않은 형식의 값이 body로 들어올 경우 걸러주는 기능이 필요    
일일이 if문으로 정의하기엔 너무 많고, 코드가 복잡해짐    
valid 어노테이션으로 자동으로 걸러주기!

* 미리 정의된 @Valid로 걸러주기

1.**dto에 어노테이션 걸어주기**

email을 받을 때, email형식을 지키는 값만 받고싶은 경우
`getEmail()` getter 메소드에 @Email 어노테이션을 걸어준다.

이후 controller 메소드에 인자 부분의 @RequestBody 앞에 @Valid 어노테이션을 붙여준다.

그리고 api를 받으면, email형식을 지키지 않은 입력에 대해서는 error를 출력한다.

2.**정규식 직접 걸기**

휴대폰 번호를 받을 때, 휴대폰 번호 형식을 지키는 값만 받고싶은 경우.

=> **받는 형식을 직접 정의하고 싶은 경우**

getter메소드에 `@Pattern` 어노테이션을 선언한다.

인자에 regexp = 값으로 원하는 정규식을 입력한다.

인터넷에서 필요한 정규식을 그때그때 검색해서 사용

* **에러 메세지를 정의하고 싶은 경우**

`public ResponseEntity user(@Valid @RequestBody User user, BindingResult bindingResult){`

`BindingResult` 타입 : 형식 오류를 일으킨 객체를 담아두는 타입

`if(bindingResult.hasErrors()){`

bindingResult객체가 오류를 발생시킨 경우, 

```java
bindingResult.getAllErrors().forEach(objectError -> {
                var field = (FieldError)objectError;
                String message = objectError.getDefaultMessage();
                
                System.out.println("field : " + field.getField());
               System.out.println(message);
```

람다식. bindingResult의 에러를 받아서 FieldError타입으로 형변환 후 어떤 객체가 오류인지 출력.

getDefaultMessage()를 통해서 미리 지정해놓은 에러 메세지를 출력할 수도 있음

`@Pattern(regexp = "^\\d{2,3}-\\d{3,4}-\\d{4}$",message = "핸드폰 번호 양식과 맞지 않습니다. 01x-xxxx-xxxx")`

`@Pattern` 어노테이션으로 에러 메세지 정의하기. message = 인자로 전달하면 됨

 **@AssertTrue() 만들기**
 
 ```java
 @AssertTrue(message = "yyyyMM의 형식에 맞지 않습니다.")   //AssertTrue 어노테이션이 달린 메소드는 is를 앞에 달아줘야 함
    public boolean isreqYearMonth(){

        //this.reqYearMonth = getReqYearMonth() + "01";

        try {
            LocalDate localDate = LocalDate.parse(getReqYearMonth() + "01", DateTimeFormatter.ofPattern("yyyyMM"));
        } catch (Exception e){
            return false;
        }
        return true;
    }
```

**Valid 어노테이션 직접 만들기**

>난 진짜 이 날먹 자기가 안다고 무지성 만들기 인강이 맘에 안들어




    


