# Facebook API를 활용한 나만의 챗봇 만들기
## TDD 테스트 코드 만들기
- 명세서 요구사항대로 일단 만들고 (전부 실패한 상태)
- 하나씩 코드를 만들어간다.
- java 용 테스트코드는 프로젝트 안의 test/java 에서 만들면 된다.
- 원래 프로젝트에 있던 **클래스명_Test.java** 식으로 명명하는게 일반적이다.
- JUnit 을 치면 JUnit Test Case 가 나온다. 
- CarTest를 만들어보자.
- 테스트메소드위에는 @ 어노테이션이 달린다.
- assertEquals("Korean Tire.", car.getTireBrand());
- "기대값", "실제값" 이렇게 비교해서 테스트하게 된다.
- 오른쪽버튼 클릭 > run as > JUnit Test (모두 테스트해보는 방법도 있다. 이건 직접해보기)

## 스프링 코드 만들기
- 스프링은 필요한 설정파일을 만들어야 한다.
- New > Spring bean configuration file > config.xml 입력
- 객체를 인스턴스화 하는 작업을 스프링에서 하자.
- id:인스턴스명 Class:실제클래스가들어있는 패키지명과 인스턴스명을 적어주자.
- 한번 만들어진 객체는 재사용가능하다. (싱글턴 패턴이거든)
- 자바파일에서 bean 클래스를 사용하려면 컨텍스트 인스턴스를 사용하면 된다. (ApplicationContext)
```java
// xml 파일을 context 화 해서 로드한다.
ApplicationContext context = new FileSystemXmpApplicationContext("/src/main/.../exam01/config.xml");
```
- 객체를 가져올 땐 getBean 을 통해 configuration 에 있는 객체값을 갖고온다.

```java
// Car car = new Car(); // --> 원래 쓰던 방식.

// 갖고올땐 object 로 갖고오기 때문에 형식을 지정해줘야 한다.(Car)
Car car = (Car)context.getBean("car");  // getBean 파라미터에는 id 값을 적어준다.
```

- xml 로 작성하면 컴파일하고 재배포할 필요가 없다.

- 객체에 값을 주입하는 걸 spring 코드로 바꿔보자.
- Car 클래스에 setTire 가 있었다.
```java
Tire tire = (Tire)context.getBean("tire");
car.setTire(tire);
```
이렇게 바꿔준다.
```xml
// car 를 생성할껀데 tire 를 참고해서 생성해주세요.
<bean id="car" class="examp01.Car">
  <property name="tire" ref="tire></property> // set 메소드 호출과 같음.
</bean>
```

테스트 코드도 위의 방식대로 바꿔보자.
```java
// CarTest.java
// 테스트 코드에서는 바로 로드할 수 있도록 지원이 된다.
@RunWidth(SpringJUnit4ClassRunner.class)  
@ContextConfiguration("config.xml");  // 읽고자하는 xml 파일. 얘가 참조하는 파일은 main 프로젝트의 config.xml 을 로드해온다.
public class CarTest {
  @Autowired // config 파일과 instace 를 자동맵핑해준다.
  Car car;  // config 파일에서 car 코드 설정되어있으면 자동주입해라.
  
  @Test
  ...
}
```

> 패키지가 없다면
> form 파일 클릭하고 porm.xml탭 > `<dependencies>` 부분을 추가해준다.
> dependency 탭에서도 추가할 수 있다고 함...

- config.xml 파일의 밑에 Namespace 탭에 context 체크한다.
- xmlns:context 가 추가되어있는 걸 볼 수 있다.
- annotation-config 가 들어가있으면 
- Car.java 에서만 autowired 하려고 하는데.
- 얘가 작동되려면 config vㅏ일에다가 annotation-config 엘리먼트를 추가하면 Car.java 파일에 자동으로 셋팅된다.

- xml 파일에서 bean 의 id 값을 빼도 동작이 된다.
- Autowired 는 **타입우선**이기 때문에 타입이 있기만 하면 정상동작된다. (cf. Resource 는 이름을 우선으로 동작)
```xml
<bean class="examp01.Car" />
```
- id 값을 다른걸 써도 정상작동!
```xml
<bean id="yeah" class="examp01.Car" />
```

- 만약 타입이 두개 다 살려진 경우라면? 에러가 난다. 두개 중 뭘 가져와야할지 모르기 때문!

```xml
<bean id="kor_tire" class="examp01.KoreanTire" />
<bean id="Ame_tire" class="examp01.AmericanTire" />
```

이럴 경우 java 파일에 `Qualifier` 어노테이션을 사용한다.
```java
@Qualifier("kor_tire")
Tire tire;
```

역전의 제어 (IoC)
- 객체 젱 방식이 IoC 컨테이너로 넘어왔다.
- 데이터를 주입하는 방법과 데이터를 찾는 방법이 가능.
- Setter, Contructor, Method Injection 등 모두 가능
- setter 메소드를 빼기 때문에 자원관리가 쉬워졌다.

## Spring Boot

- 실제로는 config 파일, 화면 구성이 복잡하다.
- 초기 환경설정이 엄청 간단하다!!!
- 스프링 프레임워크를 간단히 초기설정해준다.
- 웹 어플리케이션 개발 환경 만들 때 특화되어있다.
- Spring MVC 를 잘 작동하게 해준다.
- 톰캣이 내장되어있다.


원하는 환경설정이 가능.
- core 항목의 devTools, web 항목의 web 선택
- controller 클래스 하나 만들고

```java
package com.example.demo;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
	@RequestMapping("/hello")
	public String hello() {
		return "Hello World!!!";
	}
}
```
- localhost:8080/hello 접속하면 다음이 뜬다.

## 간단히 게시판 만들기

- in-memory db 인 H2 를 이용할 것
- packaging War 다른 was 서버에 가도실행시킬 수 있다.
- db 에 넣을 때 JPA 실제 데이터 베이스에 맵핑해주는 과정.
- dependencies 는 Web, Thymeleaf, JPA, H2 추가
- 사용자가 요청을 하면 (글 쓰겠다, 글 목록 보여줘) `MessageController.java`가 할 것임(주신 코드 복붙).
- getMapping 글 목록을 볼 때
- postMapping 글을 썼을 때
- MessageForm.java 는 validation 용
```java
package com.example.demo;

import javax.validation.constraints.Size;

public class MessageForm {
    @Size(max=80)
    private String name;
 
    @Size(min=1, max=140)
    private String text;
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public String getText() {
        return text;
    }
 
    public void setText(String text) {
        this.text = text;
    }
}
```
- html 파일은 src/main/resource/templates 에다가 생성한다.
- Thymeleaf 사용하고 싶으면 앞에 `th:text`를 이용한다.

db
- JPA 를 이용해서 java 객체와 db 의 관계를 연결할 것이다.
- entity, repository, service 구현
- 맵핑 하기 위해서 테이블 정의에 해당하는 클래스를 만들것.
- null 필요하면 nullable 추가하고 timestamp 추가... preupdate, 
- 데이트 날짜 설정할 때 팁도 있음

- repository 인터페이스
- 실제로 사용자가 전하려는 모든 메소드들이 여기 있다!

서비스
- rollback 여기서 하고
- 자동 주입되도록 해두었음
- 최근 메세지 갖고오는 메소드, 저장하는 메소드.
- db 인서트.
