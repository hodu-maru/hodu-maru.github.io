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
  
