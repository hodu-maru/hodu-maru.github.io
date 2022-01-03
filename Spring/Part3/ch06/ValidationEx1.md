# Spring
## 


**Exception 처리 예시**

```java
@ExceptionHandler(value = MethodArgumentNotValidException.class)
    public ResponseEntity MethodArgumentNotValidException(MethodArgumentNotValidException e){

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }
```

```java
    @ExceptionHandler(value = ConstraintViolationException.class)
    public ResponseEntity constraintViolationException(ConstraintViolationException e){
    
     BindingResult bindingResult = e.getBindingResult();

        bindingResult.getAllErrors().forEach(error ->
        {
            FieldError field = (FieldError) error;

            String fieldName = field.getField();
            String message = field.getDefaultMessage();
            String value = field.getRejectedValue().toString();
            
            System.out.println("================");
            System.out.println(fieldName);
            System.out.println(message);
            System.out.println(value);
        });

        
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }
```

사용자가 읽기 쉽도록 에러 메세지를 정의함

`BindingResult bindingResult = e.getBindingResult();`

`BindingResult` 타입으로 에러 메세지를 받은 다음

`bindingResult.getAllErrors().forEach(error ->`

람다식으로 해당 에러 객체의 모든 에러들에 대해

```java
FieldError field = (FieldError) error;

            String fieldName = field.getField();
            String message = field.getDefaultMessage();
            String value = field.getRejectedValue().toString();
```

`FieldError`타입으로 형변환 후, 에러의 이름, 메세지, 입력된 값을 출력함







`ConstraintViolationException` 은 어떤 필드가 잘못되었는지에 대한 정보를 담고 있음

```java
    @ExceptionHandler(value = MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e){
        
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }
    
```
