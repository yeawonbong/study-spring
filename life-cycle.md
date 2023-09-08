# Life Cycle

# 스프링 컨테이너 생명주기

![image](https://github.com/yeawonbong/study-spring/assets/75327385/26ec553e-b21e-40ab-9a71-9a39fdf56219)

- 생성시점
    - GenericXmllApplicationContext 객체를 생성 (new)할 때 메모리공간에 스프링 컨테이너가 생긴다.
- 소멸시점
    - 해당 객체를 close()로 해제시킬 때 메모리에서 소멸된다.
    - 이 때 안에 있는 Bean 객체들도 자동으로 소멸된다.
- 컨테이너가 생성되면서 같이 Bean 객체가 생성되기 때문에, 결국 스프링 컨테이너 생성 시점과 Bean 객체 생성 시점은 동일하다.

# 빈(Bean) 객체 생명주기

- 생성시점
    - 컨테이너.getBean()을 호출하여 Bean 객체가 생성된다. 사실상 컨테이너 생성시점과 동일.
    - 생성 및 의존관계 주입이 이뤄진다.
- 소멸시점
    - 컨테이너를 close()로 소멸시킬 때 함께 소멸된다.
- 즉, 스프링 컨테이너와 빈(Bean) 객체의 생명주기는 같다.

## 빈(Bean)의 생성/소멸에 동작 추가하기

두가지의 방법이 있다.

### (방법1) 인터페이스 이용

![image](https://github.com/yeawonbong/study-spring/assets/75327385/63c2e067-4868-41a3-bdfc-38258fc203ca)

- 스프링에서 제공하는 인터페이스 (구현되지 않는 껍데기)
    - InitializingBean
    - DisposableBean
- 내가 사용하는 Bean 객체에 위 인터페이스들을 구현하여 사용할 수 있다.
    
    ```java
    public class RandomService implements InitializingBean, DisposableBean {
    
    	@Autowired
    	private RandomDao randomDao;
    
    	@Override
    	public void afterPropertiesSet() throws Exception {
    		//Bean 객체 생성 시 실행되는 코드	
    	}
    
    	@Override
    	public void destroy() throws Exception {
    		//Bean 객체 소멸 시 실행되는 코드		
    	}
    
    }
    ```
    
    - Bean이 생성되는 시점, 소멸되는 시점에 특정 동작을 추가할 수 있다.
    - 가령, DB연결을 위한 ID와 PW을 박아놓고 인증받을 때, 혹은 네트워크 연결을 위한 인증, 로그 등

### (방법2) 스프링 설정파일에 기입

![image](https://github.com/yeawonbong/study-spring/assets/75327385/ccca054e-2776-4e57-9e01-33c19d62fcd5)


- 스프링 설정파일 xml의 <Bean> 태그에 `init-method`, `destroy-method` 속성 이용
- 속성값으로는 메소드 이름을 기입해주고, 해당 메소드는 해당 Bean class 안에 만들어준다.
