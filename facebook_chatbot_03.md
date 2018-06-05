# Facebook API를 활용한 나만의 챗봇 만들기

기획 어떻게 하면 좋고 구축 어떻게 하면 좋은지에 대해 얘기할 것임.

---

## 01.REST API

- **무엇을? 어떻게?** 에 대해 생각하기
- 개발하기 전, API 설계가 명확해야 한다..!!
- API 는 어디까지나 **인터페이스**. 
- 지극히 심플, 다양한 사람에게 공개할 것, 자원이 다른 쪽과 공유할 수 있는 걸 생각하기. (그게 API 거든)

### API 설계 가이드

- API 만들기 전에 서비스를 준비해놓고, (데이터)
- 어떤 정보를 필요로 하고 있는지? (블로그를 예를 들면 기사, 태그, 댓글, 카테고리...)
- 해당 자원에 어떤 작업이 필요한가? 리소스에 필요한 작업을 생각하기 (CRUD. 블로그 게시물 확인(R), 작성(C),...)
- 자원의 상하관계
- 게시물에는 코멘트를 갖고 있고, 게시물은 각종 카테고리에 속한다.
- Cool URI - 좋은 URI 를 만듭시다. (구글 다음 네이버의 로그인, 지도 서비스)
> - 쉽게 바뀌면 안된다.
> - 기존에 있는 걸 지원해준다.

### URL 설계
- 한 눈에 api 를 알 수 있는 url
> "어, 이거 API 잖아."
> 사이트 개발 디렉토리와 API 디렉토리는 분리 하기. (하위 도메인으로 만듭시다.)
> - http://example.com/api (디렉토리로 나눌 경우)
> - http://api.example.com (하위 도메인)

- api **버전**을 포함. 버전을 잘 관리하는게 중요!!!
> 개발자가 API 버전을 선택할 수 있도록. 버전의 분리가 쉬워짐
> - http://api.example.com/1/article (x)
> - http://api.example.com/v1/article (o)

- 동사보다는 명사로 구성
> http://api.example.com/v1/articles/126/comments/10

- **응용 프로그램과 언어에 의존하지 않게** 만들기
> url 끝에 .php .jsp 등 언어와 관련된 확장자 붙이지 말자.

- 자원의 관계를 한눈에 알 수 있도록 설계
- 너무 길지 않게

### HTTP 메소드 결정

REST API 에서 주로 사용하는 것은 4가지
- POST : 자원을 새로 만들 때 (create)
- GET : read
- PUT : update
- DELETE

### QR 코드 API 사용해보기

- http://goqr.me/api/
- **Just try it, create an image** 화면의 이 부분에 있는 주소를 클릭하면 qr 코드 만드는 사이트로 이동한다.
- 주소값에 parameter 값을 수정한 후 이동하면 거기에 맞는 qr 코드가 만들어진다.
> - data : qr 코드에 포함할 데이터
> - size : qr 코드 사이즈
> - format : png, jpg, gif, svg 확장자 지정 가능
> - color : 코드 칼라
> - bgcolor : 배경 칼라
- https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=채수정
- 나중에 개발할 때 만들어진 qr 코드를 db 에 저장하거나 url 자체를 img 태그의 src 속성에 url 입력하면 표시된다.

---

## 02.JSON

- Javascript Object Notation
- php ruby 등 특정 프로그램 언어에 의존하지 않음
- **키**와 **값**의 쌍을 가지고 있다.
```javascript
var person = {
  "lastName" : "수정",
  "firstName" : "체"
}
```
- 객체의 키 값으로 접근할 수 있다. `person.lastName`
- 네트워크 상에서 데이터를 주고받을 때 쉽게 사용할 수 있다.

### 영화정보를 가져와보기

- http://www.omdbapi.com
- 영화정보값을 json 형태값으로 반환해주는 api
- http://www.omdbapi.com/?t=avengers 이걸 검색해보면
- {"Response":"False","Error":"No API key provided."}
- 인증이 필요하다는 에러가 나온다. 반드시 해당하는 회사가 갖고 있는 key 값이 필요하다.
- http://www.omdbapi.com/?apikey=8122ef34&t=now+you+see+me
- 만약 결과값이 일렬로 보일 때 [크롬확장프로그램](https://goo.gl/F7RWXP)을 설치해주면 깔꼼하게 정렬되어 있는 데이터가 나온다.

이런식으로 url 을 만들어서 메세지를 로드해보는 api 를 만들어보겠음.
- MessageController.java 에서 @Controller 어노테이션이 붙었었다. 이건 클라이언트에 대해서 get/ post 를 담당하는 거라는 명시.
- API Controller 에서는 json 형식으로 데이터를 갖고와볼 것이다.
> - Client <-----> Blog (Controller, GET,POST 방식을 썼음) <-> db
> - Client <-----> REST API Blog (RestController) <-> db
- 참조 에러나면 `Ctrl + Shift + O`
- 새로운 컨트롤러를 만들고, 이번엔 `@Controller` 가 아닌 `@RestController` 어노테이션을 붙인다. (REST API 를 응답하는 놈)
- DB상의 데이터 값을 json 형태로 제공한다.
- 테스트해볼까?
```java
@RestController
public class ApiMessageController {
	
	@RequestMapping("/api/v1/messages")
    public List<String> messages(Model model) {
		// 우리가 갖고 있는 블로그의 목록을 가져온다.
		List<String> list = new ArrayList<String>();
		list.add("TEST");
		list.add("채수");
		return list;
    }
}

```

- db 에 있는 값들을 갖고와보자.
```java
@RestController
public class ApiMessageController {
	
	@Autowired
    private MessageService service;
	
	@RequestMapping("/api/v1/messages")
    public List<Message> messages(Model model) {
     // 우리가 갖고 있는 블로그의 목록을 가져온다.
		 List<Message> messages = service.getRecentMessages(100);
		return messages;
    }
}
```

---

## 03.Node.js

- Non-blocking I/O 방식을 채용해서 비동기적으로 실행하며 가볍다.
> - 기존 : 데이터베이스에 쿼리를 실행했을 때 결과가 돌아올 때까지 기다리는 시간이 필요
> - non-blocking : 쿼리를 기다리지 않고 다음 처리로 이동이 가능하다.
- 서버측의 개발을 하기 위해서 나왔다.
- NPM : Node.js 에는 필요한 모든 패키지를 관리하는 도구가 있다. 이게 바로 npm. (패키지를 도입, 갱신, 삭제 작업 수행)
- 챗봇을 만들 때 서버가 하나 필요한데
- **Client --> ChatBot API (Server) ---** 본인이 할 수 없으면 **---> 외부 Server**
- 요 server 에 가장 많이 사용되는 게 node.js

- 노드 설치하기. https://nodejs.org/en/
- 설치 후 node 와 npm 버전 확인하면 정상 설치 됐는지 확인할 수 있다.
- 윈도우 : window + node 치면 Node.js command prompt 실행하면되고
- 맥은 그냥 터미널사용하면 됨~

### Http 서버 작성

1) 작업 폴더 만들기
2) `app.js` 파일 만들고
3) 다음을 코딩한다.
```javascript
var http = require('http');
http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Hello World!');
}).listen(8300);
console.log('Server running');
```
4) 터미널에서 해당 디렉토리에 간 후 `$ node app.js` 를 실행.
5) 터미널창에 `Server running`이 뜨고, localhost:8300 에 들어가면 `Hello World!`가 뜬다.

- 외부에 있는 모듈을 가져오려면 require 를 사용한다.
- 
- createServer 안에 있는 걸 '구현부', '모듈function' 이라고 한다.
- **javascript 로 작성된 파일을 모듈이라 하고**
- **모듈군을 디렉토리로 합친 것을 패키지라고 한다.**
- 패키지 설치 
```javascript
// 작업 디렉토리에서만 설치하고 싶다면
$ npm install 패키지명
// 다른 디렉토리에서도 사용하고 싶다면
$ npm install -g 패키지명
```
- 패키지 사용
```javascript
var pkg = require("패키지명");
```
- 패키지 리스트
```javascript
$ npm list [-g]
```

### 간단한 모듈 만들고 호출하기

0) 같은 디렉토리에 파일들을 만들어야 한다.
1) 모듈 만들기
```javascript
// mod.js
exports.printBoo = function() {
    return "Hello!";
}
```

2) 모듈 호출하기
```javascript
// main.js
var mod = require('./mod.js');
console.log(mod.printBoo());
```

3) 터미널에서 node.js 실행 `$ node main.js`

### Express 모듈

- 웹 프레임워크를 만들 때 사용

---

다음시간엔~
Node.js 와 Express 를 합쳐서 REST API 를 준비할 것임
제공하고싶은 리소스 생각해보기시고, 설계해볼께요.
