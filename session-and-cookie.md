# Session, Coockie

- 세션과 쿠키는 클라이언트와 서버의 연결을 유지하는 방법이다.

## Connectionless Protocol

![image](https://github.com/yeawonbong/study-spring/assets/75327385/ed248faf-6cc8-49e0-997f-66d647a1c030)

- 웹 서비스는 HTTP프로토콜을 기반으로 하는데, HTTP프로토콜은 클라이언트와 서버 관계를 유지하지 않는 특징이 있다.
- 한 번 요청-응답 즉시 연결이 끊어진다. (서버의 효율적인 운영을 위해)
- 서버의 부하를 줄일 수 있는 장점은 있으나, 클라이언트의 요청 시마다 서버와 매번 새로운 연결이 생성되기 때문에 일반적인 로그인 상태 유지, 장바구니 등의 기능을 구현하기 어렵다.
- 이러한 Connectionless Protocol의 불편함을 해결하기 위해서 세션과 쿠키를 이용한다.

## 세션(Session)과 쿠키(Cookie)

- 세션과 쿠키는 클라이언트와 서버의 연결 상태를 유지해주는 방법이다.

### 세션

- 서버에서 연결 정보를 관리

### 쿠키

- 클라이언트에서 연결 정보를 관리

# Session

- 예시 - 로그인을 하면(로그인 응답을 받으면) 세션을 생성해서 서버에 갖고 있는다. 이후 요청에는 해당 세션을 이용한다.
- HttpServletRequest 혹은 HttpSession 객체를 사용한다.
    - HttpServletRequest와 HttpSession의 차이점은 거의 없으며, 단지 세션객체를 얻는 방법에 차이가 있을 뿐이다

### HttpServletRequest을 이용한 세션 사용

```java
public String memLogin(Member member, HttpServletRequest request) {

	Member mem = service.memberSearch(member);
	HttpSession session = request.getSession();
	session.setAttribute("member", mem); //속성이름, 속성값 - 회원정보를 담는다. 

	return "/member/loginOK";
}
```

- 파라미터로 HttpServletRequest를 받은 후 getSession()으로 세션을 얻음

### HttpSession을 이용한 세션 사용

```java
public String memLogin(Member member, HttpSession session) {

	Member mem = service.memberSearch(member);
	session.setAttribute("member", mem); //속성이름, 속성값 - 회원정보를 담는다. 
	return "/member/loginOK";
}
```

- 파라미터로 HttpSession를 받아 세션 사용

### 세션 삭제(세션에 속성을 삭제)

- 세션을 삭제하는 방법은 세션에 저장된 속성이 더 이상 필요 없을 때 이루어지는 과정으로 주로 로그아웃 또는 회원탈퇴 등에 사용된다.

```java
@RequestMapping("/logout")
public String memLogout(Member member, HttpSession session) {

	session.invalidate();

	return "/member/logoutOk";
}
```

```java
@RequestMapping(value = "/remove", method = RequestMethod.POST)
public String memRemove(Member member, HttpServletRequest request) { 

	service.memberRemove(member);

	HttpSession session = request.getSession(); 
	session.invalidate();
	
	return "/member/removeOk"; 
}
```

### 세션 주요 메소드 및 플로우

| 세션 메소드 | 기능 |
| --- | --- |
| getId() | 세션 ID를 반환한다. |
| setAttribute() | 세션 객체에 속성을 저장한다. |
| getAttribute() | 세션 객체에 저장된 속성을 반환한다. |
| removeAttribute() | 세션 객체에 저장된 속성을 제거한다. |
| setMaxInactiveInterval() | 세션 객체의 유지시간을 설정한다. |
| getMaxInactiveInterval() | 세션 객체의 유지시간을 반환한다. |
| invalidate() | 세션 객체의 모든 정보를 삭제한다. |

![image](https://github.com/yeawonbong/study-spring/assets/75327385/15cbf98b-7843-4bf3-a9c1-89c211e89e26)

# Cookie

- 세션과 동일한데, 클라이언트쪽에 저장.

### 쿠키 생성 - addCookie()

- 쿠키를 생성할 때는 생성자에 두 개의 파라미터를 넣어주는데, 첫번째는 쿠키이름, 두번째는 쿠키값을 넣어
준다.

```java
@RequestMapping("/main")
public String mallMain(Mall mall, HttpServletResponse response){

	Cookie genderCookie = new Cookie("gender", mall.getGender()); //쿠키 생성

	if(mall .isCookieDel()) { 
		genderCookie.setMaxAge(0); 
		mall.setGender(null);
	} else{ 
		genderCookie.setMaxAge(60*60*24*30); //쿠키의 시간 설정 - 30일동안 쿠키 유지
	}
	response.addCookie(genderCookie);

	return "/mall/main"; 
}

```

- mallMain()에서 쿠키를 생성하고, 파라미터로 받은
HttpServletResponse에 쿠키를 담고 있다.

### 쿠키 사용 - @CookieValue 어노테이션

```java
@RequestMapping("/index")
	public String mallIndex(Mall mall,
							@CookieValue(value="gender", required=false) Cookie genderCookie, HttpServletRequest request) {
		if(genderCookie != null) mall.setGender(genderCookie.getValue());
		return "/mall/index"; 
}
```

- @CookieValue 어노테이션의 value 속성은 쿠키 이름을 나타내는데, 만약 value에 명시한 쿠키가 없을 경우 익셉션이 발생한다.
- 익셉션을 막는 방법이 있는데, 바로 required 속성이다.
    - Required 속성은 기본값으로 true를 가지고 있는데 required가 true인 경우 value값에 해당하는 쿠키가 없으면 익셉션이 발생한다. 따라서 required 속성값을 false로 설정해서 value값에 해당하는 쿠키가 없어도 익셉션이 발생하지 않도록 한다.

# 특징

- 실무에서 중요한 정보는 Session을 더 많이 사용한다.
    - 서버에 저장되면서 보안이 더 강하기 때문
- Cookie는 더 간단하고 가벼운 정보들을 저장. 서버에 부하를 줄일 수 있다는 장점.
