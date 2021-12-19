# Spring
## 4.Spring 시작하기

### ObjectMapper

`objectMapper`는 `main`이 아닌 `test`폴더에서 진행한다.

`test`의 `ApplicationTests` 클래스에서 수행.


* `Object Mapper`는 텍스트 형태의 json을 object로 바꿔주고, object를 다시 텍스트 형태의 json으로 바꿔준다.

`var objectMapper = new ObjectMapper();`를 통해 직접 objectMapper를 객체로써 사용할 수 있다.

```java
//object -> text
        var user = new mapperUser("jinhyun",25);

        var text = objectMapper.writeValueAsString(user);
                                        //object를 string으로 바꾸는 메소드
        System.out.println(text);
```

위와 같이 `objectMapper`의 `writeValueString()`메소드를 사용해서 text를 찍어보면, **에러가 뜬다.**


*  **object에서 text로 바꾸는 과정에서 objectMapper는 get메소드를 사용한다.**

     사용하는 mapperUser클래스에서 변수의 getter메소드를 생성해주면, 정상적으로 text가 출력된다.
     
     
반대로 text -> object를 하는 과정을 수행해보면
```java
var objectUser = objectMapper.readValue(text,mapperUser.class);
                                        //첫번째는 바꾸려는 json text를, 두번째는 바꾸려는 클래스 타입.class를 넣어준다.
        System.out.println(objectUser);
```

`objectMapper`객체의 readValue()메소드를 사용해, 첫번째 인자로는 **바꿀 json text 객체를**, 두번째는 바꾸려는 **` 클래스 타입.class `** 를 넣어준다.

이대로 프로그램을 동작하면, 에러가 난다.


* **text에서 object로 바꾸는 과정에서 objectMapper는 `default생성자`를 사용한다.**

  현재 mapperUser에 정의된 생성자는 name과 age를 인자로 받는 생성자이므로, default생성자가 아니다.
  
  
  ```java
  public mapperUser(){
        this.name = null;
        this.age = 0;
    }
  ```

위와 같이 인자를 받지 않고, `string`의 default값인 `null`과 `int`의 default값인 0을 주는 default생성자를 정의한 뒤 다시 돌리면 정상 작동한다.


**내가 작성한 클래스가 objectMapper를 사용할 경우, 절대 임의의 get이란 이름이 앞에 붙은 메소드를 선언해선 안된다!**
 







