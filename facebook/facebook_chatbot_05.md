# Facebook API를 활용한 나만의 챗봇 만들기

git 에 heroku 프로젝트 업로드하기

heroku git:remote -a sooheroku

push 하는 것만으로도 자동으로 배포된다.
$ git push heroku master

web dyno 수 = CPU 수
```
$ heroku ps:scale web=1 [서버명]
$ heroku ps:scale web=1 sooheroku
```
- ps : 현재 작동하는 상태
- scale web=1 : 중지되어있는 서버 start
- scale web=0 : stop
- 장시간 사용하지 않으면 자동으로 sleep 상태가 된다.

sooheroku.herokuapp.com


어플리케이션 구동 상태 확인
```
$ heroku ps -a [서버명]
$ heroku ps -a sooheroku

Free dyno hours quota remaining this month: 550h 0m (100%)
For more information on dyno sleeping and how to upgrade, see:
https://devcenter.heroku.com/articles/dyno-sleeping

No dynos on ⬢ sooheroku
```

## Heroku + Spring Boot

- Spring Boot Project 만들기
> New > Other > Spring Starter Project > Packaging 을 Jar 로 만든다. (웹용)
> Next 하는 건 
- Controller 추가
```java
package com.example.demo;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
	@RequestMapping("/hello")
	public String hello() {
		return "Hello World";
	}
}

```
restcontroller 만들면 
별다른 옵션없이 json 포맷으로 전달할 수 있다.
ASpring boot,
Spring boot starter web 만 dependencies 에 살리자.


- Resource 추가 (json 전달)
- git add *
- git commit -m "Comment"
- heroku apps:create [app name]
- heroku git:remote -a [app name] --> remote 변경
- git push heroku master
- --app [app name] 앱 등록
- 
- heroku logs -t

챗봇에서 데이터 request 되면 중간 business logic 에서 처리한 후, 외부의 데이터를 가져오면 된다.

remote:        [INFO] ------------------------------------------------------------------------
remote:        [INFO] BUILD SUCCESS
remote:        [INFO] ------------------------------------------------------------------------
remote:        [INFO] Total time: 5.840 s
remote:        [INFO] Finished at: 2018-06-12T11:02:00Z
remote:        [INFO] ------------------------------------------------------------------------
remote: -----> Discovering process types
remote:        Procfile declares types     -> (none)
remote:        Default types for buildpack -> web

web 으로 빌드 되는지 확인하자. war 파일은 안될 거임. jar 파일로 하자.

---
제대로 된 Rest api 코드를 만들어보자.
사용자가 입력한 파라미터 값대로 다이나믹하게 적용하는게 가능하다.

```java
@RestController
@RequestMapping("api/v1/memo")
public class ApiMemoResource {
  @RequestMapping("{author}")
  public List<User> get(@PathVariable String author) {
    return Arrays.asList(new User("Author", author));
  }
}
```
{author} 임의 값을 @PathVariable 로 바로 변수에 저장.

---

## ChatBot 개요

어떤 챗봇을 만들어야 할까?
- 재미있고, 꼭 필요한 것

왜 만들어야 할까?
- 비용 절감
- 시간, 에너지 절약

잘 되어있는 사례
- Nordstorm

챗봇의 특징
- 기존 플랫폼과의 통합되어 접근성이 쉽다. (앱을 새로 깔 필요가 없음. 네이버 톡톡으로 주문하고 네이버페이로 결제가능)
- 메신저 앱을 통해 여러 프로필에 접근 가능

챗봇의 유형
- 대답해주려면 [지역, 시간, 조건] 같은 규칙이 필요한 규칙 집합 기반 챗봇
- 인공지능이 있는 기계 학습 기반 챗봇

AI
- Bot : 인터넷으로 연결된 환경에서 업무 자동화 어플리케이션
- ChatBot : 자연어를 써서 커뮤니케이션할 수 있는 Bot
- AI Assistant : 유저의 다양한 요청에 서비스 할 수 있는 Bot

- 자연어 처리 NLU Understanding 말을 이해하고
- NLG Generate 말을 만드는게 목표임
- 로그 파일을 분석해야 한다.
- 자연어 처리 엔진에서 질문의 가장 핵심적인 단어들만 필터링 할 수 있다.
- about.mattermost.com --> Messaging Platform Server 를 제공한다.
- 사용자 별로 thread 화 해야 한다.

주고받는 대화의 인터페이스를... 글로만 해야할까??
