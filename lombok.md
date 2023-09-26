# Lombok

- Lombok이란 Java의 라이브러리로, 반복되는 메소드를 Annotation을 사용해서 자동으로 작성해주는 라이브러리다.
- 보통 DTO나 Model, Entity의 경우 여러 속성이 존재하고 이들이 가지는 프로퍼티에 대해서 Getter나 Setter, 생성자 등을 매번 작성해줘야 하는 경우가 있다. 이러한 부분을 자동으로 만들어주는 라이브러리다.
- Lombok을 이용해서 작성한 코드는 컴파일 과정에서 Annotation을 이용해서 코드를 생성하고 .class에 자동 컴파일 된다.

# 대표적 어노테이션

### @Getter, @Setter

아래 코드처럼 get메소드와 set메소드를 생성할 필요 없이 @getter @setter 어노테이션을 이용하면 필드에 대한 getter, setter를 간단하게 자동으로 생성해준다. 필드의 이름이 X 라면 getX(),  setX()이다.

```java
package com.example.lombok.dto;
 
import lombok.Getter;
import lombok.Setter;
 
@Getter
@Setter
public class UserDTO {
	
	private String id;
 
	private String name;
}

//이런 식으로 전체 필드가 아닌 특정 필드에만 setter메소드를 추가할 수 있다. 
package com.example.lombok.dto;
 
import lombok.Getter;
import lombok.Setter;
 
@Getter
public class UserDTO {
	
	private String id;
	
	@Setter
	private String name;
}
```

### @NonNull

메소드나 생성자의 매개변수에 @NonNull을 사용하면 lombok이 null check를 한다.

### @ToString

ToString이 붙은 클래스는 lombok이 toString() 메소드를 생성해준다. 기본적으로는 클래스 이름과 각 필드에 대한 값을 출력한다. 출력을 원하지 않는 변수에 @ToString.Exclude 어노테이션을 붙여주면 출력을 제외할 수 있다. 부모 클래스에 대해도 toString을 적용시키고자 한다면 부모 클래스에 @ToString(callSuper = true) 를 적용한다.

```java
package com.example.lombok.dto;
 
import lombok.ToString;
 
@ToString
public class UserDTO {
 
	@ToString.Exclude
	private String id;
 
	private String name;
}
```

### @EqualsAndHashCode

lombok이 클래스에 대한  equals(Object other)와 hashCode()를 만든다.

### @Data

모든 필드에 대해 @ToString, @EqualsAndHashCode, @Getter를, 모든 non-final 필드에 대해 @Setter를 설정하고 @RequiredArgsConstructor를 설정해주는 단축 Annotation이다.

### @NoArgsConstructor

매개변수가 없는 기본 생성자를 생성한다. 만약 불가능 하다면(final 필드 떄문에) 컴파일 에러가 난다. 만약 @NoArgsConstructor(force = true)를 사용하면 컴파일 에러를 발생시키는 대신 모든 final 필드는 기본값(0, false, null)로 초기화된다.

```java
package com.example.lombok.dto;
 
import lombok.NoArgsConstructor;
import lombok.ToString;
 
@Getter
@NoArgsConstructor
public class UserDTO {
 
	private String id;
 
	private String name;
	
	// public UserDto() {}
}
```

### @RequiredArgsConstructor

초기화되지 않은 모든 final 필드, @NonNull 필드에 대한 생성자를 생성해주며 의존성을 주입받을 수 있다. @NonNull 필드의 경우 null check 구문 또한 생성할 수 있다.

```java
package com.example.lombok.dto;
 
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.ToString;
 
@Getter
@RequiredArgsConstructor
public class UserDTO {
 
	@NonNull
	private String id;
 
	private final String name;
    
        // public UserDTO(String id, String name){ 
	//	this.id = id; this.name = name;
	// }
}
```

### @AllArgsConstructor

모든 필드에 대한 생성자를 만들어준다. 마찬가지로 의존성을 주입받을 수 있으며, @NonNull 필드에 대한 null check 구문을 생성할 수 있다.

```java
package com.example.lombok.dto;
 
import lombok.AllArgsConstructor;
import lombok.NonNull;
import lombok.ToString;
 
@Getter
@AllArgsConstructor
public class UserDTO {
 
	private String id;
 
	private String name;
	
	// public UserDTO(String id, String name) {
	//	this.id = id;
	//	this.name = name;
	//}
}
```

### @Builder

빌더를 자동으로 작성해준다. 클래스에 작성하면 모든 필드에 대한 빌더를 만들어주며, 원하는 필드에 대해서만 빌더를 작성하고 싶은 경우 생성자를 작성하고 그 위에 @Builder를 붙여주면 된다.

```java
package com.example.lombok.dto;
 
import lombok.Builder;
import lombok.Getter;
 
@Getter
public class UserDTO {
 
	private String id;
 
	private String name;
 
	@Builder
	public UserDTO(String id, String name) {
		this.id = id;
		this.name = name;
	}
}
```

```java
package com.example.lombok.controller;
 
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
 
import com.example.lombok.dto.UserDTO;
 
@Controller
public class HomeController {
 
	@GetMapping("/")
	public String home(Model model) {
 
		UserDTO userDTO = UserDTO.builder().id("id").name("name").build();
		model.addAttribute("userDTO", userDTO);
 
		return "home";
	}
}
```

위와 같이 @Builder 어노테이션을 사용할 수 있으며 userDTO 객체의 id 필드 값은 'id', name 필드 값은 'name'으로 초기화되어 있다.
