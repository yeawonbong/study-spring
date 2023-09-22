# Controller

## @ResponseBody

- 기존 컨트롤러에서 데이터를반환하기 위해서는 `@ResponseBody` 어노테이션을 활용해야 한다.
- JSON 형태로 데이터를 반환할 수 있다.

## @RequestParam

- Http 요청 파라미터의 이름으로 바인딩하여 그 값을 변수에 저장한다.
- HttpServletRequest의 request.getParameter의 기능과 동일하다고 보면 된다.
- HTTP 파라미터 이름이 변수 이름과 같으면 @RequestParam의 value를 생략 가능하다.

## 동작과정

![image](https://github.com/yeawonbong/study-spring/assets/75327385/42bc7921-9c4a-431b-87d8-982c946e1dd4)

1. Client는 URI 형식으로 웹 서비스에 요청을 보낸다.
2. DispatcherServlet이 요청을 처리할 대상을 찾는다.
3. HandlerAdapter을 통해 요청을 Controller로 위임한다.
4. Controller는 요청을 처리한 후에 객체를 반환한다.
    - 이 때, viewResolver 대신에 HttpMessageConverter가 동작한다.
        - HandlerAdapter와 Controller가 요청을 주고받는 시점에 동작
        - HttpMessageConverter에는 여러 Converter가 등록되어 있고, 반환해야 하는 데이터에 따라 사용되는 Converter가 달라진다.
            - 단순 문자열인 경우에는 StringHttpMessageConverter
            - 객체인 경우에는 MappingJackson2HttpMessageConverter
        - Spring은 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해 적합한 HttpMessageConverter를 선택하여 이를 처리한다.
5. 반환되는 객체는 Json으로 Serialize되어 사용자에게 반환된다.

## 예시

```java
@Controller
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping(value = "/users")
    public @ResponseBody ResponseEntity<User> findUser(@RequestParam("userName") String userName){
        return ResponseEntity.ok(userService.findUser(user));
    }
    
    @GetMapping(value = "/users/detailView")
    public String detailView(Model model, @RequestParam("userName") String userName){
        User user = userService.findUser(userName);
        model.addAttribute("user", user);
        return "/users/detailView";
    }
}
```

- ResponseEntity로 감싸서 반환하고 있고, JSON으로 반환하기 위해 `@ResponseBody`라는 어노테이션을 붙여주고 있다.

# RestController

- `@RestController`는 @Controller에 @ResponseBody가 추가된 것이다.
- 주 용도는 JSON형태로 객체 데이터를 반환하는 것이다.
- REST API를 개발할 때 주로 사용하며, 객체를 ResponseEntity로 감싸서 반환한다.

## 동작과정

![image](https://github.com/yeawonbong/study-spring/assets/75327385/44710559-7ec0-445c-8664-93eae0cd50a1)

1. Client는 URI 형식으로 웹 서비스에 요청을 보낸다.
2. DispatcherServlet이 요청을 처리할 대상을 찾는다.
3. HandlerAdapter을 통해 요청을 RestController로 위임한다.
4. RestController는 요청을 처리한 후에 객체를 반환한다.
5. 반환되는 객체는 Json으로 Serialize되어 사용자에게 반환된다.

## 예시

```java
@RestController
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping(value = "/users")
    public ResponseEntity<User> findUserWithResponseEntity(@RequestParam("userName") String userName){
        return ResponseEntity.ok(userService.findUser(user));
    }
}
```
