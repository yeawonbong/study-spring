# redirect

## redirect란?

![image](https://github.com/yeawonbong/study-spring/assets/75327385/c43cbc96-a9ac-4e57-83f0-a057ccd8d9de)

- 컨트롤러에서 뷰를 분기하는 방법
- 지금의 페이지에서 특정 페이지로 전환하는 기능
- 예를 들어 어떤 페이지로 진입하기 위한 어떤 조건이 있는데, 그 조건이 만족하지 않았을 때 다른 페이지로 페이지를 전환시키는 기능이다.

## 사용방법

```java
@RequestMapping(value = "/modifyForm")
	public String modifyForm(Model model, HttpServletRequest request) {
		
		HttpSession session = request.getSession();
		Member member = (Member) session.getAttribute("member");
		
		
		if(null == member) {
			return "redirect:/"; //메인페이지로 리다이렉트
		} else {
			model.addAttribute("member", service.memberSearch(member));
		}
		
		return "/member/modifyForm";
	}
```

- 조건을 넣어주어 redirect 해준다.
- 키워드는 `redirect:`

# interceptor

## interceptor란?

![image](https://github.com/yeawonbong/study-spring/assets/75327385/d89fe06e-406d-4134-ba5e-cda7d5229e74)

- redirect를 사용해야 하는 경우가 많은 경우 HandlerInterceptor를 이용할 수 있다. (redirect를 좀 더 편리하게 사용할 수 있는 방법)
- 컨트롤러 실행 전/후에 특정 작업을 가능하게 하는 방법

## HandlerInterceptor 인터페이스

```java
public interface HandlerInterceptor {

    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {
        
        return true;
    }

    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable ModelAndView modelAndView) throws Exception {
    }

    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable Exception ex) throws Exception {
    }
}
```

- org.springframework.web.servlet에 포함된 인터페이스
- 인터페이스이기 때문에, 구현해서 사용해야 한다.
- 3가지 메소드를 갖고 있다.

### preHandle()

- 실무에서 가장 많이 쓰이는 메소드.
- 컨트롤러가 호출되기 전에 실행된다.
- 컨트롤러 이전에 처리해야 하는 전처리 작업이나 요청 정보를 가공하거나 추가하는 경우에 주로 사용
- 3번째 파라미터인 handler 파라미터는 핸들러 매핑이 찾아준 컨트롤러 빈에 매핑되는 HandlerMethod라는 새로운 타입의 객체로써, @RequestMapping이 붙은 메소드의 정보를 추상화한 객체이다.
- 반환 타입은 boolean
    - 반환값이 true이면 다음 단계로 진행이 되지만, false라면 작업을 중단하여 이후의 작업(다음 인터셉터 또는 컨트롤러)은 진행되지 않는다.

### postHandle()

- 컨트롤러를 호출된 후에 실행된다.
- 컨트롤러에서 작업을 진행하다가 중간에 예외가 발생하면 postHandle은 호출되지 않는다.

### afterCompletion()

- 컨트롤러, 뷰까지 모든 작업이 완료된 후에 실행된다.
- 요청 처리 중에 사용한 리소스를 반환할 때 주로 사용된다.
- postHandler과 달리 컨트롤러에서 작업을 진행하다가 중간에 예외가 발생하더라도 afterCompletion은 반드시 호출된다.

## HandlerIntercepterAdapter

- HandlerIntercepter는 인터페이스라, 사용 시 모두 구현해야하는 불편함이 있다.
- 이에 Spring에서는 HandlerIntercepter의 구현체인 HandlerIntercepterAdapter라는 클래스를 제공한다.
- 사용 시 HandlerIntercepterAdapter 클래스를 상속받아 새로운 클래스를 생성한 후 스프링 설정파일로 설정해주어 기능을 사용한다.

## 사용방법

1. Interceptor 클래스 생성
    
    ```java
    public class MemberLoginInterceptor extends HandlerInterceptorAdapter {
    
    	@Override
    	public boolean preHandle(HttpServletRequest request,
    			HttpServletResponse response, Object handler) throws Exception {
    		
    		HttpSession session = request.getSession(false);
    		if(session != null) {
    			Object obj = session.getAttribute("member");
    			if(obj != null) 
    				return true;
    		}
    		
    		response.sendRedirect(request.getContextPath() + "/");
    		return false;
    	}
    	
    	@Override
    	public void postHandle(HttpServletRequest request,
    			HttpServletResponse response, Object handler,
    			ModelAndView modelAndView) throws Exception {
    		
    		super.postHandle(request, response, handler, modelAndView);
    	}
    	
    	@Override
    	public void afterCompletion(HttpServletRequest request,
    			HttpServletResponse response, Object handler, Exception ex)
    			throws Exception {
    		
    		super.afterCompletion(request, response, handler, ex);
    	}
    }
    ```
    
2. Spring 설정파일에 명시 (servlet-context.xml)
    - interceptor를 적용할 경로 명시하는 방법
        
        ```java
        <interceptors>
        		<interceptor>
        			<mapping path="/member/modifyForm"/>
        			<mapping path="/member/removeForm"
        			<beans:bean class="com.bs.lec21.member.MemberLoginInterceptor"/>
        		</interceptor>
        	</interceptors>
        ```
        
    - 모든경로에 적용하고, 제외시킬 경로 명시하는 방법
        
        ```java
        <interceptors>
        		<interceptor>
        			<mapping path="/member/**"/>
        			<exclude-mapping path="/member/joinForm"/>
        			<exclude-mapping path="/member/join"/>
        			<exclude-mapping path="/member/loginForm"/>
        			<exclude-mapping path="/member/login"/>
        			<exclude-mapping path="/member/logout"/>
        			<exclude-mapping path="/member/modify"/>
        			<exclude-mapping path="/member/remove"/>
        			<beans:bean class="com.bs.lec21.member.MemberLoginInterceptor"/>
        		</interceptor>
        	</interceptors>
        ```
        

# filter (javax.servlet)

## filter란?

![image](https://github.com/yeawonbong/study-spring/assets/75327385/a32df2ba-9a58-45ac-aefe-954b10beb7d7)

- filter는 자바 서블릿에서 제공하는 기능이다.
- 요청이 DispatcherServlet에 의해 다뤄지기 전, 후에 동작한다.
- 또한 Filter는 FilterChain을 통해 여러 필터가 연쇄적으로 동작하게 할 수 있다.
    
    ![image](https://github.com/yeawonbong/study-spring/assets/75327385/0c43a50b-85fe-4daf-be5c-3ba4ba5a5c72)

    

## Filter 인터페이스

```java
public interface Filter {
 
    public default void init(FilterConfig filterConfig) throws ServletException {}
 
    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;
 
    public default void destroy() {}
```

- javax.servlet에 포함된 인터페이스
- 인터페이스이기 때문에, 구현해서 사용해야 한다.
- 3가지 메소드를 갖고 있다.

### **init()**

- 필터가 생성될 때 수행되는 메소드
- 웹 컨테이너가 1회 init()을 호출하여 필터 객체를 초기화하면 이후 요청들은 doFilter()를 통해 처리된다.

### doFilter()

- Request, Response가 필터를 거칠 때 수행되는 메소드
- 모든 요청, 응답이 DispatcherServlet으로 전달되기 전에 웹 컨테이너에 의해 실행되는 메소드이다.

### destroy()

- 필터가 소멸될 때 수행되는 메소드
- 필터 객체를 제거하고 사용하는 자원을 반환하기 위한 메소드
- 웹 컨테이너가 1회 destroy()를 호출하여 필터 객체를 종료하면 이후에는 doFilter에 의해 처리되지 않는다.

## filter 사용예시

- 필터는 주로 요청에 대한 인증, 권한 체크 등을 하는데에 쓰인다.
- 구체적으로 들어온 요청이 DispatcherServlet에 전달되기 전에 헤더를 검사해 인증 토큰이 있는지 없는지, 올바른지 올바르지 않은지 등을 검사할 수 있다.

## 사용 방법

1. 필터클래스를 만든다
    
    ```java
    @Slf4j
    public class FirstFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
            log.info("FirstFilter가 생성 됩니다.");
        }
    
        @Override
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
            log.info("==========First 필터 시작!==========");
            filterChain.doFilter(servletRequest, servletResponse);
            log.info("==========First 필터 종료!==========");
        }
    
        @Override
        public void destroy() {
            log.info("FirstFilter가 사라집니다.");
        }
    }
    ```
    
    - `filterChain.doFilter()`에서 요청-작업-응답이 이뤄지며, 전후로 실행할 동작을 추가할 수 있다.
2. 만든 필터클래스를 Spring Bean으로 등록한다 - java Config사용
    
    ```java
    @Configuration
    public class Config{
    
        @Bean
        public FilterRegistrationBean firstFilterRegister() {
            FilterRegistrationBean registrationBean = new FilterRegistrationBean(new FirstFilter());
            return registrationBean;
        }
    }
    ```
    
    - `FilterRegistrationBean`을 이용해 필터를 등록한다.
3. 스프링이 시작하면서 init()메소드 실행
4. 요청이 들어오면 doFilter()메소드 실행
5. 종료 시 destroy()메소드 실행

### 두개 이상의 필터 사용 시

1. (위와 이어서) 두번째 필터 생성
    
    ```java
    public class SecondFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
            log.info("SecondFilter가 생성 됩니다.");
        }
    
        @Override
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
            log.info("==========Second 필터 시작!==========");
            filterChain.doFilter(servletRequest, servletResponse);
            log.info("==========Second 필터 종료!==========");
        }
    
        @Override
        public void destroy() {
            log.info("SecondFilter가 사라집니다.");
        }
    }
    ```
    

2. 빈으로 등록

```java
@Configuration
public class Config{

    @Bean
    public FilterRegistrationBean firstFilterRegister() {
        FilterRegistrationBean registrationBean = new FilterRegistrationBean(new FirstFilter());
        registrationBean.setOrder(1);
        return registrationBean;
    }

    @Bean
    public FilterRegistrationBean secondFilterRegister() {
        FilterRegistrationBean registrationBean = new FilterRegistrationBean(new SecondFilter());
        registrationBean.setOrder(2);
        return registrationBean;
    }
}
```

- 이 때 필터의 동작 순서를 `FilterRegistrationBean.setOrder()`로 설정할 수 있다.

# Interceptor vs Filter

![image](https://github.com/yeawonbong/study-spring/assets/75327385/f7a1e6d2-7f69-41cd-bd48-493e95c04205)

- 필터(Filter)는 특정 요청과 컨트롤러에 관계없이 전역적으로 처리해야 하는 작업이나 웹 어플리케이션에 전반적으로 사용되는 기능을 구현할 때 사용하고,
- 인터셉터(Interceptor)는 클라이언트의 요청과 관련된 작업에 대해 추가적인 요구사항을 만족해야 할 때 주로 사용한다.
