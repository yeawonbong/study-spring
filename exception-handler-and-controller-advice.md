# ExceptionHandler, ControllerAdvice

# @ExceptionHandler

@ExceptionHandler같은 경우는 @Controller, @RestController가 적용된 Bean내에서 발생하는 예외를 잡아서 하나의 메서드에서 처리해주는 기능을 한다.

```jsx
@RestController
public class MyRestController {
    ...
    ...
    @ExceptionHandler(NullPointerException.class)
    public Object nullex(Exception e) {
        System.err.println(e.getClass());
        return "myService";
    }
}

```

- @ExceptionHandler라는 어노테이션을 쓰고 인자로 캐치하고 싶은 예외클래스를 등록해주면 끝난다.
    - `@ExceptionHandler({ Exception1.class, Exception2.class})` 이런식으로 두 개 이상 등록도 가능하다.
- 위의 예제에서 처럼하면 MyRestController에 해당하는 Bean에서 NullPointerException이 발생한다면, @ExceptionHandler(NullPointerException.class)가 적용된 메서드가 호출될 것이다.

## 주의사항/알아 둘 것

- Controller, RestController에만 적용가능하다. (@Service같은 빈에서는 안됨.)리턴 타입은 자유롭게 해도 된다. (Controller내부에 있는 메서드들은 여러 타입의 response를 할 것이다. 해당 타입과 전혀다른 리턴 타입이어도 상관없다.)
- @ExceptionHandler를 등록한 Controller에만 적용된다. 다른 Controller에서 NullPointerException이 발생하더라도 예외를 처리할 수 없다.
- 메서드의 파라미터로 Exception을 받아왔는데 이것 또한 자유롭게 받아와도 된다.

# @controllerAdvice

- @ExceptionHandler가 하나의 클래스에 대한 것이라면, @ControllerAdvice는 모든 @Controller 즉, 전역에서 발생할 수 있는 예외를 잡아 처리해주는 annotation이다.

```jsx
@RestControllerAdvice 
public class MyAdvice { 
		
		@ExceptionHandler(CustomException.class) 
		public String custom() { 
				
				return "hello custom"; 
		} 
}
```

- 위와 같이 새로운 클래스파일을 만들어서 annotation을 붙이기만 하면 된다. 그 다음에 @ExceptionHandler로 처리하고 싶은 예외를 잡아 처리하면 된다.
- 별도의 속성값이 없이 사용하면 모든 패키지 전역에 있는 컨트롤러를 담당하게 된다.
