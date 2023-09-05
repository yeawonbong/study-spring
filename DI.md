# DI (Dependency Injection)

# Dependency Injection(DI)란?

- 의존주입
- DI는 Spring만의 기능은 아니고, 일종의 프로그래밍 방법론이다. 주로 OOP에서 적용
- 객체를 다 독립화 시켜서 기능을 꼈다 뺐다 유연하게 이용. 외부에서 객체를 주입
- Java 코드 예시) 배터리 일체형, 배터리 분리형
    
    ![image](https://github.com/yeawonbong/study-java/assets/75327385/c18b5bad-2449-4731-bffd-12cfd5b6e690)

    

# Spring DI 설정 방법

- XML에 명시하여 객체 생성 및 의존 주입(DI)
    - constructor-arg 태그 (생성자 파라미터)
    
    ![image](https://github.com/yeawonbong/study-java/assets/75327385/89c2f13a-e36d-402f-9753-6c8747e96781)

    
    ```java
    <bean ~~~~ >
    	<constructor-arg ref="{refArg}"/> 
    </bean>
    ```
    

# 다양한 의존객체 주입(DI)

## 생성자를 이용한 DI

(위 그림 참고)

- bean 태그를 이용하여 dao 객체 생성
- 서비스 객체 생성하면서 파라미터로 객체 주입 - constructor-arg 태그 이용

## setter를 이용한 DI

![image](https://github.com/yeawonbong/study-java/assets/75327385/39448efc-c659-4e6e-b2b9-c2c52ecc8732)


- property 태그 이용
- 규칙
    - name: set 떼고 소문자로,
    - value: 파라미터 들어오는 값

## List타입 의존 객체 주입

- 들어오는 값이 List타입일 경우

![image](https://github.com/yeawonbong/study-java/assets/75327385/8831d14d-55a4-4bd9-a62a-11156779769f)


## Map타입 객체 주입

- 들어오는 값이 Map타입일 경우

![image](https://github.com/yeawonbong/study-java/assets/75327385/6796bf11-31ec-4339-b680-ff6c67ed84cc)


# 스프링 설정 파일 분리

![image](https://github.com/yeawonbong/study-java/assets/75327385/9f2d36be-6626-4e51-8add-bf4a583d17d4)


보통 기능별로 분리한다.

- DAO 객체
- 서비스객체
- DB관련 객체

## 적용 방법

- 배열형태로 xml파일들 모두 삽입
    
    ```java
    String[] appCtxs = {"classpath:appCtx1.xml", "classpath:appCtx2.xml", "classpath:appCtx3.xml"};
    
    GenericXmlApplicationContext(appCtxs); // 파라미터에 배열형태로 넣어준다
    ```
    
- 그런데 xml파일에도 import가 있어서, import태그로 한 xml파일에 여러 xml파일을 import해주면 그 한 xml파일만 파라미터로 넣어줘도 된다. *~~(c 헤더처럼 하나에 모아서 사용 가능한듯)~~*

# 의존객체 자동주입 *~~[re]~~*

## 의존객체 자동주입이란?

![image](https://github.com/yeawonbong/study-java/assets/75327385/3f7b461b-2b6e-424c-8ea6-b56c5b74327c)


- 스프링 설정 파일에서 의존 객체를 주입할 때 <constructor-org> 또는 <property> 태그로 의존 대상 객체를 명시하지 않아도, 스프링 컨테이너
가 자동으로 필요한 의존대상 객체를 찾아 의존대상객체가 필요한 객체에 주입해주는 기능
- `@Autowired`와 `@Resource` 어노테이션을 이용해서 쉽게 구현할 수 있다.

## 의존객체 자동주입 방법

- Java 코드에 어노테이션을 달면 된다.
- 컨테이너xml에 추가
    
    ```jsx
    <context:annotation-config/>
    ```
    

### @Autowired

- 주입하려고 하는 **객체와 타입이 일치**하는 객체를 자동으로 주입한다.
- 생성자, setter, 메소드에 모두 사용할 수 있다.
- 일반적으로 실무에 가장 많이 쓰인다. ~~*권장되지는 않음 - 순환참조 이슈*~~

### @Resource

- 주입하려고 하는 **객체와 이름이 일치**하는 객체를 자동으로 주입한다.
- 생성자에 사용 X, setter, 메소드에 사용할 수 있다.

### @Inject

- @Autowired와 거의 비슷게 @Inject 어노테이션을 이용해서 의존객체들을 자동으로 주입할 수 있다.
- @Autowired와 차이점 이라면 @Autowired의 경우 required 속성을 이용해서 의존 대상 객체가 없어도 Exception을 피할 수 있지만, @Inject의 경우 required 속성을 지원하지 않는다.
    - required - 의존객체 자동 주입 체크
        
        ![image](https://github.com/yeawonbong/study-java/assets/75327385/4025d031-ef40-4294-afc6-a63e20dbc64f)

        
        - 자동주입을 할 객체가 없는 객체라면 Exception이 발생한다.
        - 이 때 Exception을 발생시키지 않고 예외처리를 해주는 옵션이다.
        - 객체가 있으면 주입하고, 없으면 그냥 넘어가는 것
        - 사실상 쓸 일이 없어야함..

## 의존객체 선택

### qualifier - 의존객체 선택

![image](https://github.com/yeawonbong/study-java/assets/75327385/e6150773-6be5-4ee0-827f-390fb63f48fb)


- 타입이 같은 세 개의 Bean이 있을 때 qualifier 태그로 의존객체를 선택할 수 있다.
