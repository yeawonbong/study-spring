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

![image](https://github.com/yeawonbong/study-spring/assets/75327385/41b5d795-8ed0-4144-8caa-599877fa857f)


![image](https://github.com/yeawonbong/study-spring/assets/75327385/df37b9d5-7c22-4ae7-acab-cec29dab57de)


- 일반적으로는 명시적인 방법을 많이 이용한다. (스프링 설정파일 직접명시)

