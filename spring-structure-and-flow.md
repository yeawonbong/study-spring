# Spring 프로젝트 폴더 구조

![image](https://github.com/yeawonbong/study-spring/assets/75327385/6c24cce1-d9b2-4289-8670-efe07d8d13be)


1. java파일
    - java파일들이 위치한다. 주로 패키지로 묶어서 관리.
    - 웹 애플리케이션에서 사용되는 Controller, Service, DAO객체들이 위치한다.
2. resources 
    - 자원을 관리하는 폴더로, 스프링 설정파일(XML) 또는 프로퍼티 파일 등이 관리된다.
    - JSP파일을 제외한 html, css, js파일등이 위치한다.
3. webapp
    - 웹과 관련된 파일들(스프링 설정파일, JSP파일, HTML파일 등..)이 위치한다.
4. spring폴더
    - 스프링 컨테이너를 생성하기 위한 스프링 설정파일이 위치한다.
    - `applicationContext.xml` 등의 설정파일이 여기에 위치함.
5. views 폴더 : View로 사용될 JSP파일이 위치한다.
6. pom.xml파일 : 메인 레파지토리에서 프로젝트에 필요한 라이브러리를 내려받기 위한 메이븐 설정 파일

## 프로젝트 구조 예시

![image](https://github.com/yeawonbong/study-spring/assets/75327385/5050fbf1-0c37-4310-8c53-19c0646b3046)


- dto(Data Transfer Object) 객체
    - Book.java
- dao 객체
    - BookDao.java
- 서비스 객체
    - BookRegisterService.java
    - BookSearchService.java
# 웹 프로그래밍을 구축하기 위한 설계 모델

## Model1 방식

- 서비스, DAO, view가 한 파일에 작성된다.

![image](https://github.com/yeawonbong/study-spring/assets/75327385/0e0f7b48-3226-4de3-950e-5b28c038d6b9)


1. 브라우저(클라이언트)에서 서버(WAS)로 요청(request)
2. 서버에서 처리 → DB 접근하여 가져옴
3. 가져온 데이터를 가공하여 브라우저에 응답(response) - html 형태로 응답

### 장점

- html파일 안에 java코드와 태그 등을 한 문서에 넣어서 개발속도가 빠름

### 단점

- 여러 언어가 한 파일에 작성 → 유지보수 차원에서 좋지 않다.

## Model2 방식

- Model1의 단점을 보완한 방식, MV C
- 모듈화, Spring Web Application에 기본적으로 사용되는 모델

![image](https://github.com/yeawonbong/study-spring/assets/75327385/7bf7a3f4-343a-4d57-815a-34e46f6aa34a)


1. 브라우저에서 요청을 Controller가 받아서 해당하는 Service로 연결해준다. 
    - Service는 하나하나의 기능들이라고 보면 된다.
2. DAO를 이용한 Model이라는 객체로 DB와 통신한다. 
3. 통신하여 얻은 데이터를 다시 컨트롤러에 넘긴다
4. 컨트롤러는 View라는 객체로 클라이언트에 응답한다. (JSP파일)

### 장점

- 기능별로 분리되어있기때문에 유지보수가 수월하다.

# Spring MVC 프레임워크 설계 구조 및 흐름

- 스프링 없이 Servlet만으로 구현했으면 View, ViewResolver, HandlerMapping가 하는 기능들도 다 직접 처리를 했어야 하는 부분들이다.
- 스프링을 사용함으로써 우리는 Controller 부분만 신경쓸 수 있게 되었다.

![image](https://github.com/yeawonbong/study-spring/assets/75327385/acc9b8a8-1b37-410a-92ac-b5f442e36b01)


## 1. `DispatcherServlet`

- 클라이언트에서 요청을 받는 객체
- 요청을 받아서 `HandlerMapping`에 던진다.

## 2. `HandlerMapping`

- 많은 Controller들 중 가장 적합한 Controller를 선택해주는 역할
- 알맞은 Controller를 선택하고 다시 `DispatcherServlet`으로 온다.
- `DispatcherServlet`은 `HandlerAdapter`에 요청을 보낸다.

## 3. `HandlerAdapter`

- 한 Controller 안에 있는 많은 Method들 중에 클라이언트의 요청에 적합한 Method를 찾아주는 역할.

## 4. `Controller`

- 찾은 다음 `Controller`에 요청하고, 이에 대한 결과를 `Model`이라는 데이터로 가져온다.
    - Controller 안에는 Service, DAO, DB가 있다.
- 다시 `DispatcherServlet`으로 온다.

## 5. `ViewResolver`

- **(REST API에서는 이부분이 사용 X)**
- html 코드로 이루어진 View라고 생각하면 된다.
- Controller를 거쳐 가져온 Model데이터와 view 정보로 가장 적합한 JSP 문서(view)를 찾아주는 역할.
- (처리결과를 출력할 view 선택)
- 다시 `DispatcherServlet`으로 온다.

## 6. `View`

- JSP 문서로 클라이언트에 응답

# DispatcherServlet 설정

## web.xml

- 웹 환경설정하는 파일
- 서블릿 등록, 초기 파라미터 등록 등 설정할 수 있다.
- 웹 어플리케이션에서 최초 사용자의 요청이 발생하면 가장 먼저 DispatcherServlet이 사용자의 요청을 받는다.
- 따라서 개발자는 DispatcherServlet을 서블릿으로 등록 해주어야 하고, 사용자의 모든 요청을 받기 위해서 서블릿 맵핑 경로는 ‘/’로 설정한다.

![image](https://github.com/yeawonbong/study-spring/assets/75327385/41b5d795-8ed0-4144-8caa-599877fa857f)


![image](https://github.com/yeawonbong/study-spring/assets/75327385/df37b9d5-7c22-4ae7-acab-cec29dab57de)


- 일반적으로는 명시적인 방법을 많이 이용한다. (스프링 설정파일 직접명시)

# Controller 객체 설정

- DispatcherServlet에서 HandlerMapping(Controller 탐색), HandlerAdapter(Method Member 탐색)를 통해 Controller로 요청을 보내고,
- 컨트롤러에서 데이터를 Model객체, View객체로 응답을 보낸다. DispatcherServlet으로 보낸다.
- HandlerMapping, HandlerAdapter는 Spring Container에 자동으로 생성이 되지만, Controller는 개발자가 직접 만들어줘야 한다.

## `annotation-driven 태그` - 스프링 설정파일

```java
// servlet-context.xml

<annotation-driven/>
```

- 스프링 설정 파일 `servlet-context.xml`에 `<annotation-driven/>` 라는 태그를 넣어주면
- SpringContainer를 사용하기 위한 여러 클래스들이 Bean 객체로 스프링 설정파일에 존재하게 된다.

## `@Controller 어노테이션` - 사용할 객체 클래스

```java
@Controller
public class HomeController {
	...
}
```

- 일반적인 클래스로 작성하고, 컨트롤명 위에 `@Controller` 어노테이션을 붙여주면 해당 클래스는 Controller로 기능을 하게 된다.
- 단, 위 과정 (스프링 설정파일에 annotation-driven 태그 작성)이 선행 되어야만 한다.
- `HandlerMapping`이 `@Controller` 어노테이션을 보고 요청에 알맞는 컨트롤러 탐색

## `@RequestMapping 어노테이션`

```java
// ex) 요청: http://localhost:8090/test/success

@RequestMapping("/success")
public String success(Model model) {
	...
	return "success";
}
```

- 컨트롤러 안에 있는 메소드명 위에 `@RequestMapping` 어노테이션을 붙여주면 해당 메소드는 HandlerMapping를 통해 특정 요청url과 매핑이 된다.
- `HandlerAdapter`가 `@RequestMapping` 어노테이션의 문자를 보고 요청에 알맞는 메소드 탐색

## Model 타입의 파라미터

- 컨트롤러 뒤에는 Service, DAO, DB 등이 있겠다.
- 그러한 처리를 하고 난 뒤 `DispatcherServlet`으로 다시 올릴 때 사용되는 객체가 Model, View.
- 예시
    
    ```java
    // Controller class의 코드
    @RequestMapping("/success")
    public String success(Model model) {
    	...
    	return "success";
    }
    
    // View의 코드
    model.setAttribute("tempData", "model has data!!");
    ```
    
    - 컨트롤러의 메소드의 파라미터로 Model객체를 넣어준다.
    - View파일 안의 코드에서 `{모델객체}.setAttribute({속성이름}, {속성값});`로 사용한다. (데이터를 Model 객체에 담아서 전달한다.)
    - 개발자는 Model 객체에 데이터를 담아 DispatcherServlet에 전달할 수 있다.
    - DispatcherServlet에 전달된 Model데이터는 View에서 가공되어 클라이언트에게 응답처리된다.
    

# View 객체

(pass)
