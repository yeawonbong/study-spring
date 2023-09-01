# Spring Container, Bean
  
# Spring 프로젝트 폴더 구조

- java 폴더
    - 앞으로 만들어지는 자바 파일들이 관리되는 폴더
    - 순수 자바 코드들
- resources 폴더
    - 자원을 관리하는 폴더로, 스프링 설정파일(XML) 또는 프로퍼티 파일 등이 관리된다.
    - 스프링 경우 `applicationContext.xml`이 여기에 위치함.
- java, resources 폴더는 스프링 프레임워크의 기본 구조를 이루는 폴더로, 그대로 구조를 따라야 한다.

# Spring 컨테이너 생성 및 Bean객체 호출 과정

![image](https://github.com/yeawonbong/study-spring/assets/75327385/be6fad4a-10c6-470d-9239-ef2e12afd133)

## pom.xml로 라이브러리 연결

- pom.xml 파일은 메이븐 설정파일로, 메이븐은 라이브러리를 연결해준다.
    - 필요한 라이브러리를 명시하면 이를 다운로드해서 사용한다.
- 빌드를 위한 플랫폼이다.

## applicationContext.xml로 Bean 생성

- 이 파일 안에 Bean의 정보들이 명시되어있고, 이를 토대로 Bean을 생성한다.
- 스프링은 컨테이너 안에 객체(Bean)를 생성하여 모두 모아두고, 필요할 때마다 꺼내 쓰는데, 여기서 스프링 컨테이너에 Bean을 만들어주는 역할이 `applicationContext.xml`
- 예시
    
    ```xml
    <bean id="{id}" class="{pakage}.{classname}"/>
    ```
    
    - id는 키값을 입력
    - class는 해당 bean과 매핑될 class 풀네임을 입력

## GenericApplicationContext로 컨테이너 생성

```java
GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:applicationContext.xml");
```

- ApplicationContext는 컨테이너에 접근하기 위한 데이터타입이다.
    - 파라미터로 applicationContext resource를 적어준다. (applicationContext.xml)
- applicationContext.xml 파일을 이용하여 Container를 만들었다고 생각하면 된다 (ctx == Container)
- 컨테이너를 생성하면, Bean 생성은 컨테이너가 알아서 한다. (일일이 new로 생성 X)

## 컨테이너 통하여 Bean 접근 - getBean()

```java
{변수} = ctx.getBean("{id}", {데이터타입}); 

//ex
customClass cust = ctx.getBean("id", customClass.class);
cust.method();

ctx.close();
```

- getBean() 메서드를 이용하여 객체(Bean)를 가져와서 사용한다.
- 컨테이너는 사용 후 닫아주기.

# Bean의 범위

![image](https://github.com/yeawonbong/study-spring/assets/75327385/38975193-e09f-44d7-a824-ccc01628cdd1)

## 싱글톤 (Singleton)

- Bean을 생성하여 사용할 때 싱글톤으로, 동일한 객체를 가리킨다.
- 동일한 타입에 대해 하나의 Bean만 생성된다.

## 프로토타입 (Prototype)

- 호출할 때마다 같은 데이터타입이지만 다른 객체를 만드려는 경우
- scope 속성을 명시해주어 getBean할 때마다 새로운 객체를 생성하게 한다.
- 흔히 쓰이지는 않는다.

# 정리

- 원래 OOP 언어에서 객체를 생성할 때 new라는 키워드로 메모리에 로드해서 사용을 했었다.
- 하지만 Spring을 이용하면, Spring에 있는 applicationContext.xml라는 Spring 설정 파일을 이용하여 객체(Bean)를 생성 (메모리에 로드)할 수 있다.
- 그 객체를 컨테이너의 getBean() 메서드를 이용하여 가져올 수 있다.
- Bean을 호출할때마다 (getBean()) 가리키는 객체는 싱글톤으로 동일하다.
