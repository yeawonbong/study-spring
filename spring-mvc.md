# 프로젝트 전체 구조

![image](https://github.com/yeawonbong/study-spring/assets/75327385/baee1b7e-0fe8-4cd9-96bc-d1d8143d33d2)

1. java파일: java파일들이 위치한다. 주로 패키지로 묶어서 관리. 웹 애플리케이션에서 사용되는 Controller, Service, DAO객체들이 위치한다.
2. resources : JSP파일을 제외한 html, css, js파일등이 위치한다.
3. webapp : 웹과 관련된 파일들(스프링 설정파일, JSP파일, HTML파일 등..)이 위치한다.
4. spring폴더 : 스프링 컨테이너를 생성하기 위한 스프링 설정파일이 위치한다.
5. views 폴더 : View로 사용될 JSP파일이 위치한다.
6. pom.xml파일 : 메인 레파지토리에서 프로젝트에 필요한 라이브러리를 내려받기 위한 메이븐 설정 파일

# 스프링 프로젝트 흐름 정리

![image](https://github.com/yeawonbong/study-spring/assets/75327385/ecc4991a-04fb-4d87-a8c0-c55776e94f08)

1. 사용자의 모든 요청은 DispatcherServlet이 처음 받는다.
2. DispatcherServlet → HandlerMapping → DispatcherServlet 
    - 객체에 요청에 부합하는 Controller를 탐색한 후DispatcherSevlet에 알려준다.
    - 프로젝트에 존재하는 모든 Controller 객체를 검색하는데, 이 때 판단 조건이 `@Controller` 어노테이션.
3. DispatcherServlet → HandlerAdapter → 해당 Controller의 메소드 탐색하여 실행
    - 객체에 요청에 부합하는 메소드를 탐색한 후 해당 Controller객체의 메소드를 실행한다.
    - 이 때 `HandlerAdapter`가 메소드에 붙어있는 `@RequestMapping` 어노테이션의 문자를 보고 요청에 알맞는 메소드 선택.
4. Controller → HandlerAdapter로 ModelAndView 객체 반환.
    - Controller객체의 메소드가 실행된 후 Controller객체는 HandlerAdapter객체에 ModelAndView객체를 반환하는데, 여기에는 응답에 필요한 데이터와 뷰(JSP파일)가 담겨있다.
5. HandlerAdapter → DispatcherServlet로 ModelAndView 객체 반환.
6. DispatcherServlet → ViewResolver → View
    - ModelAndView정보를 토대로 적합한 JSP 문서(view)를 찾고, JSP문서로 응답.

# 스프링 프로젝트 설정

## web.xml

![image](https://github.com/yeawonbong/study-spring/assets/75327385/babb2ca5-6a02-4b85-9862-8f9df2ad8362)

- 웹 어플리케이션 환경설정하는 파일
- 서블릿 등록, 초기 파라미터 등록 등 설정할 수 있다.
- 개발자는 DispatcherServlet을 서블릿으로 등록 해주어야 하고, 사용자의 모든 요청을 받기 위해서 서블릿 맵핑 경로는 ‘/’로 설정한다.
    - 웹 어플리케이션에서 최초 사용자의 요청이 발생하면 가장 먼저 DispatcherServlet이 사용자의 요청을 받기 때문

## application-context.xml (servlet-context.xml)

- 스프링 설정파일
- 프로젝트에 필요한 객체 (Bean)를 생성하고 조립한다.
