 Annotation을 이용한 Spring 설정

# XML파일을 Java파일로 변경하기

- XML코드가 아닌, Java코드의 Annotation를 이용해서 Spring 컨테이너 생성하기
    
    ![image](https://github.com/yeawonbong/study-spring/assets/75327385/547feb03-2ba5-4d1f-864a-a6fb229b1830)

    

```java
@Configuration
public class MemberConfig {

// <bean id="StudentDao" class="ems.member.dao.StudentDao"></bean>
	@Bean
	public StudentDao studentDao() {
		return new StudentDao();
	}

// <bean id="registerService" class="ems.member.service.StudentRegisterService">
//     <constructor-arg ref="studentDao"></constructor-arg>
// </bean>
	@Bean
	public StudentRegisterService registerService() {
		return new StudentRegisterService(studentDao());
	}
	
	...

} 
```

## `@Configuration`

- bean 설정 클래스인 java config class에 명시해준다.
- 즉, spring container를 만드는 데 사용되는 클래스라는 것을 명시해주는 어노테이션

## `@Bean`

- 기존 XML 파일에서 <bean> 태그로 bean 객체를 생성하는 것처럼, java config로 작성할 경우, 해당 메서드에 `@Bean` 어노테이션을 명시해준다.
- 즉, spring bean을 만드는 데 사용되는 메소드라는 것을 명시해주는 어노테이션

# JavaConfig을 이용한 Spring 컨테이너 사용

## AnnotationConfigApplicationContext()

```java
//XML설정파일 사용 시
GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:applicationContext.xml");

//Java Config파일 사용 시
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MemberConfig.class);
```

- XML설정파일 시 GenericXmlApplicationContext의 역할과 동일
- 파라미터: 해당 Config Class

# 변환 예시 (xml vs javaConfig)

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
 		http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="studentDao" class="ems.member.dao.StudentDao" ></bean>
	
	
	<bean id="registerService" class="ems.member.service.StudentRegisterService">
		<constructor-arg ref="studentDao" ></constructor-arg>
	</bean>
	
	<bean id="modifyService" class="ems.member.service.StudentModifyService">
		<constructor-arg ref="studentDao" ></constructor-arg>
	</bean>
	
	<bean id="deleteService" class="ems.member.service.StudentDeleteService">
		<constructor-arg ref="studentDao" ></constructor-arg>
	</bean>
	
	<bean id="selectService" class="ems.member.service.StudentSelectService">
		<constructor-arg ref="studentDao" ></constructor-arg>
	</bean>
	
	<bean id="allSelectService" class="ems.member.service.StudentAllSelectService">
		<constructor-arg ref="studentDao" ></constructor-arg>
	</bean>
	
	<bean id="dataBaseConnectionInfoDev" class="ems.member.DataBaseConnectionInfo">
		<property name="jdbcUrl" value="jdbc:oracle:thin:@localhost:1521:xe" />
		<property name="userId" value="scott" />
		<property name="userPw" value="tiger" />
	</bean>
	
	<bean id="dataBaseConnectionInfoReal" class="ems.member.DataBaseConnectionInfo">
		<property name="jdbcUrl" value="jdbc:oracle:thin:@192.168.0.1:1521:xe" />
		<property name="userId" value="masterid" />
		<property name="userPw" value="masterpw" />
	</bean>
	
	<bean id="informationService" class="ems.member.service.EMSInformationService">
		<property name="info">
			<value>Education Management System program was developed in 2015.</value>
		</property>
		<property name="copyRight">
			<value>COPYRIGHT(C) 2015 EMS CO., LTD. ALL RIGHT RESERVED. CONTACT MASTER FOR MORE INFORMATION.</value>
		</property>
		<property name="ver">
			<value>The version is 1.0</value>
		</property>
		<property name="sYear">
			<value>2015</value>
		</property>
		<property name="sMonth">
			<value>1</value>
		</property>
		<property name="sDay">
			<value>1</value>
		</property>
		<property name="eYear" value="2015" />
		<property name="eMonth" value="2" />
		<property name="eDay" value="28" />
		<property name="developers">
			<list>
				<value>Cheney.</value>
				<value>Eloy.</value>
				<value>Jasper.</value>
				<value>Dillon.</value>
				<value>Kian.</value>
			</list>
		</property>
		<property name="administrators">
			<map>
				<entry>
					<key>
						<value>Cheney</value>
					</key>
					<value>cheney@springPjt.org</value>
				</entry>
				<entry>
					<key>
						<value>Jasper</value>
					</key>
					<value>jasper@springPjt.org</value>
				</entry>
			</map>
		</property>
		<property name="dbInfos">
			<map>
				<entry>
					<key>
						<value>dev</value>
					</key>
					<ref bean="dataBaseConnectionInfoDev"/>
				</entry>
				<entry>
					<key>
						<value>real</value>
					</key>
					<ref bean="dataBaseConnectionInfoReal"/>
				</entry>
			</map>
		</property>
	</bean>
	
</beans>
```

```java
package ems.member.configration;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import ems.member.DataBaseConnectionInfo;
import ems.member.dao.StudentDao;
import ems.member.service.EMSInformationService;
import ems.member.service.StudentAllSelectService;
import ems.member.service.StudentDeleteService;
import ems.member.service.StudentModifyService;
import ems.member.service.StudentRegisterService;
import ems.member.service.StudentSelectService;

@Configuration
public class MemberConfig {

	@Bean
	public StudentDao studentDao() {
		return new StudentDao();
	}
	
	@Bean
	public StudentRegisterService registerService() {
		return new StudentRegisterService(studentDao());
	}
	
	@Bean
	public StudentModifyService modifyService() {
		return new StudentModifyService(studentDao());
	}
	
	@Bean
	public StudentSelectService selectService() {
		return new StudentSelectService(studentDao());
	}
	
	@Bean
	public StudentDeleteService deleteService() {
		return new StudentDeleteService(studentDao());
	}
	
	@Bean
	public StudentAllSelectService allSelectService() {
		return new StudentAllSelectService(studentDao());
	}
	
	@Bean
	public DataBaseConnectionInfo dataBaseConnectionInfoDev() {
		DataBaseConnectionInfo infoDev = new DataBaseConnectionInfo();
		infoDev.setJdbcUrl("jdbc:oracle:thin:@localhost:1521:xe");
		infoDev.setUserId("scott");
		infoDev.setUserPw("tiger");
		
		return infoDev;
	}
	
	@Bean
	public DataBaseConnectionInfo dataBaseConnectionInfoReal() {
		DataBaseConnectionInfo infoReal = new DataBaseConnectionInfo();
		infoReal.setJdbcUrl("jdbc:oracle:thin:@192.168.0.1:1521:xe");
		infoReal.setUserId("masterid");
		infoReal.setUserPw("masterpw");
		
		return infoReal;
	}
	
	@Bean
	public EMSInformationService informationService() {
		EMSInformationService info = new EMSInformationService();
		info.setInfo("Education Management System program was developed in 2015.");
		info.setCopyRight("COPYRIGHT(C) 2015 EMS CO., LTD. ALL RIGHT RESERVED. CONTACT MASTER FOR MORE INFORMATION.");
		info.setVer("The version is 1.0");
		info.setsYear(2015);
		info.setsMonth(1);
		info.setsDay(1);
		info.seteYear(2015);
		info.seteMonth(2);
		info.seteDay(28);
		
		ArrayList<String> developers = new ArrayList<String>();
		developers.add("Cheney.");
		developers.add("Eloy.");
		developers.add("Jasper.");
		developers.add("Dillon.");
		developers.add("Kian.");
		info.setDevelopers(developers);
		
		Map<String, String> administrators = new HashMap<String, String>();
		administrators.put("Cheney", "cheney@springPjt.org");
		administrators.put("Jasper", "jasper@springPjt.org");
		info.setAdministrators(administrators);
		
		Map<String, DataBaseConnectionInfo> dbInfos = new HashMap<String, DataBaseConnectionInfo>();
		dbInfos.put("dev", dataBaseConnectionInfoDev());
		dbInfos.put("real", dataBaseConnectionInfoReal());
		info.setDbInfos(dbInfos);
		
		return info;
	}
	
}
```

# Java Config 파일 분리

### 보편적인 분리 체계 (기능별로 분리)

- DAO - Data Access Object
- Service - 서비스 객체들이 모여있음
- DB - DB관련된 기능이 모여있음
- utils

## 분리된 Config 파일들을 이용해서 컨테이너 생성

```java
AnnotationConfigApplicationContext ctx = 
	new AnnotationConfigApplicationContext(MemberConfig1.class, MemberConfig2.class, MemberConfig3.class);
```

해당 config 객체들을 파라미터로 나열해준다. 

## 또다른 방법, `@import`

![image](https://github.com/yeawonbong/study-spring/assets/75327385/d6a9f255-76a7-484e-8aa6-e284f73ccc40)


```java
@Configuration
@Import({MemberConfig2.class, MemberConfig3.class})
public class MemberConfig1{
	...
}

---

//사용
AnnotationConfigApplicationContext ctx = 
	new AnnotationConfigApplicationContext(MemberConfig1.class);
```

- 하나의 config 파일에 다른 config 파일들을 import
- 배열 형식으로 기입한다.
- 그럼 컨테이너 생성 시 파라미터에 config파일 하나만 넣으면 된다.

## `@Autowired`로 Bean 참조

```java
@Autowired
SampleBean sampleBean;
```

- config 파일을 분리하더라도, 프로젝트가 실행되면 생성되는 Bean들이 하나의 컨테이너에 담기기 때문에 다른 config 파일에 있는 Bean이라도 참조할 수 있다. (자동 주입)
