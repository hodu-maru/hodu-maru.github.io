# Spring
## Server to Server 통신


**`@Service`**

들어온 request에 대해 어떤 처리를 할까 에 대한 어노테이션이다.

들어온 request에 대한 처리를 실제로 하는 부분을 다른 클래스로 빼내어, 코드를 간소화할 수 있다.

```java

@RequestMapping(value="/getUsers") 
  public String getUsers(Model model) { 
  ArrayList arrayListOfUser = new ArrayList<>(); 
  for(int indexOfUser = 0; indexOfUser < 100; indexOfUser++) { 
    User user = new User(); 
    user.setUserId(indexOfUser); 
    user.setUserName("UserName"+indexOfUser);
    arrayListOfUser.add(user);
    }
  model.addAttribute("users",arrayListOfUser); 
  return "user"; 
}

출처: https://onlyformylittlefox.tistory.com/13 [Trace - 리다양의 개발 세상]

```
uri로 `/getUsers` 라는 request가 들어오면 동작하는 메서드이다.

model을 받아, arrayList를 생성한 다음 100개의 user객체를 생성해 번호와 이름을 붙여준 다음 arrayList에 추가한다.

이후 100개의 user객체들이 담긴 arrayList를 model에 등록하는 내용이다. 

위 로직을 바깥으로 빼서 정의한 다음, 간편하게 사용해보자.

**1.Service 인터페이스 만들기**

우선 Service 인터페이스를 정의한다.

```java
  public Interface UserService {
    public ArrayList getUsers();
   }
```

`getUser()`를 구현하도록 하는 UserService 인터페이스이다. `getUser()`는 인자는 받지 않고, arrayList를 반환할 것이다.

**2.Service 인터페이스를 실제로 구현하는 클래스 만들기**

다음은 실제로 `getUser()`를 구현하는 클래스를 만든다.

```java
@Service
public class UserServiceImpl implements UserService { 
  
  @Override
  public ArrayList getUsers(){
    ArrayList arrayListOfUser = new ArrayList<>();
    for(int indexOfUser = 0; indexOfUser < 100; indexOfUser++) { 
      User user = new User();
      user.setUserId(indexOfUser); 
      user.setUserName("UserName"+indexOfUser); 
      arrayListOfUser.add(user);
      }
    return arrayListOfUser;
    }
}
```

`getUser()`를 실제로 구현하는 클래스이다.

해당 클래스에 `@Service` 어노테이션을 붙어주어야 controller에서 해당 클래스를 사용할 수 있다.


**3.Controller에서 Service 사용하기**

```java

@Controller
public class UserController {
  
  @AutoWired
  private UserService userService;
  
  @RequestMapping("value = "/getUsers")
  public String getUsers(Model model) {
    model.addAttributes("users",userService.getUsers());
    return "user";
    }
}
```

다음 실제 Controller에서 미리 정의한 service를 사용한다.

**우선 `@AutoWired`를 통해 미리 만들어둔 UserService 인터페이스 타입의 객체를 선언해, 스프링에 등록시켜 둔다.**

이후 메소드에서 `userService.getUsers()`를 호출해 간편하게 user를 생성하고, model에 등록시킨다.

