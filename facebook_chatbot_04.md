# *Not complete*

express 모듈

node 에서 웹 응요프로그램 만들 때 많이 쓰는 워크 프레임

경량 서버를 구성할 것이다.

개발환경
node.js 
express 프레임워크
express-generator 사용하기 쉬운 템플릿을 제공해준다?

node.js + express 의 장점
페이스북 api랑 개발하기 편하다.

패키지 버전 확인
$ npm show express version
4.16.3

package.json 파일에서
모든 설정파일을 관리해준다.

$ npm install
package.json 에 적혀있는 패키지들이 설치되고
node_modules 폴더가 생긴다.

npm show express version

npm start


- express myapp
- cd myapp

- npm start
localhost:3000

localhost:3000/users 함 확인해보기

app.js 열어봅시다.

http request 를 확인해볼 수 있는 도구가 있다.
- cURL (맥에는 기본으로 설치되어있다.)

json 데이터를 웹에서 쉽게 볼 수 있는 사이트
- Advanced REST client (크롬 확장기능)
- JSON Formatter (크롬 확장기능)

./myapp/app.js 파일을 열어보자.
다음을 추가해보자.
```javascript
var samples = require('./routes/samples');
...
app.use('/samples', samples);
```

./myapp/routes 폴더에 다음을 추가하기
```javascript
// myapp/routes/samples.js
var express = require("express");
var router = express.Router();

router.get('/', function(req, res, next) {
}
... 
```


curl 사용하기
바로 http 요청을 실행해볼 수 있음

curl http://localhost:3000/samples/hello
하면 바로 가져온다.

post 테스트하고 싶다면
body 안에 데이터를 넣어서 보낸다.
curl -d "card=Saumsung Card" http://localhost:3000/samples

router.post('/', function(req, res, enxt) {
  var param = {"값" : "POST 메소드", "body 값" : req.body.card};
  ...
  res.send(param);
}

myapp 프로젝트를 만들면 

### package.json
name : 어플리케이션 이름 (이걸 기반으로 어플리케이션의 정보가 표시된다.) 유니크한 이름으로 만들자~
version
scripts : 실제 노드쓸 때 스크립트가 기동이 되는거임.
  start : node ... 이런식으로 초기 구동환경설정 가능
dependencies : 어떤 패키지들이 필요한지. 

여기를 바탕으로 node.js 가 구동된다.

routes 미들웨어 함수가 작성되는 곳.
views 실제 html 템플릿 파일이 저장되는 곳.

### app.js

어플리케이션이 기동되는 파일
다양한 서브 url 같은걸 설정.
에러 핸들링 선언

### Routing

클라이언트의 요구에 따라 응답하는 주소.
```javascript
// 미들웨어 설정
var indexRouter = requires('./routes/index');
...
// 라우팅 설정
app.use('/', indexRouter);
```

### 미들웨어

라우트가 되어 있는 걸 처리하는 것.
// get 방식을 할 때 실제 동작하는 부분 --> 미들웨어 함수
router.get(...);
req, res 객체에 대한 처리 부분이 여기 들어간다.

> - req : 클라이언트 정보
> - res : 페이지 응답 정보
> - next : 콜백 작업에 필요한 데이터를 넘겨줄 수 있다.

파라미터 값에 따라 반응하는 API 를 만들어볼 것임.
```javascript
router.get('/hello/:place', function (req, res, next) {
  var param = {"result":"Hello " + req.params.place + " !");
  ...
  res.send(param);
});
```

## 해로쿠 Heroku

클라우드 서비스
인터넷 상에서 사용할 수 있는 다양한 서비스

클라우드 서비스는 4가지 형태가 있다.
어디까지를 클라우드로 제공하는지에 따라 분류가 다르다.

IaaS
- Network, hard ware, os
- 자유롭게 개발할 수 있다. 낮은 단계 원하는 버전을 선택할 수 있고, 종류도 정할 수 있고. 땅만 빌려가는거여~
- 진입장벽이 좀 있다. 개발하는 데 전문 지식이 필요하다.
- Amazon elastic server

PaaS (Platform as a service)
- Network, hard ware, os, middle ware
- 인프라, 개발환경이 세트로 제공되는 환경.
- 별도로 다른걸 설치하지 않아도 된다.
- 입사 맨 처음에 개발환경을 셋팅하겠지. 이걸 이미 제공되어 있어서바로 일을 할 수 있다.
- Google App Engine, **Heroku**, Microsoft Azure, IBM Bluemix

SaaS
- Network, hard ware, os, middle ware, applications
- gmail, blog, naver mail 같은 거.
- 개발 기술에 상관없이 사용
- 이미 준비되어 있는 서비스이기 때문에 자유도가 떨어진다.

DaaS
- 가상머신을 데스크탑으로 쓸 수 있다.
- 인터넷만 있으면 클라우드 환경에 저장된다.
- 네트워크 환경이 아무리 빨라도, local 파일이 아니기 때문에 데이터 전송 느리다..

=> 이 중에서 플랫폼을 제공하는 PaaS를 사용하겠다.

Heroku 를 사용하는 이유
- 챗봇 서버는 로컬에서 작동이 안된다. 이 서비스를 모두가 접근할 수 있어야 하기 때문에.
- 페이스북 기반에서 메신저를 사용한다.
- 웹 사이트를 인터넷에 공개할 수 있도록 서버를 제공.

### Heroku 앱 생성
heroku.com 사이트에서 만들거나, 터미널에서 생성하는 2가지 방법이 있다.

#### 1) heroku 웹 사이트에서 만들기

1) http://heroku.com 가입
2) 로그인 > Dashboard 에서 만들기

#### 2) 터미널에서 앱 만들기
```
$ heroku apps:create [app name]
$ heroku apps:create sooheroku
```

예제 node.js 어플리케이션 생성

```javascript
// 서버이름은 유니크하게!
$ express --session --ejs --css stylus sooheroku

// 디렉토리 이동 후
cd sooheroku

$ npm install
```

자 이 서버파일을 heroku 클라우드에 올려볼껀데, 그 전에 heroku 클라우드가 제일 먼저 실행하는 명령어를 지정하는 파일을 만들어준다.
Procfile 파일을 만들어 다음을 입력해준다. (확장자 없음)
```
web: node ./bin/www
```
- web : 웹어플리케이션임을 선언. 
- node ./bin/www : node 로 ./bin/www 파일을 실행해주세요.

Toolbelt 설치한다.
우리 컴퓨터에서 사용하기 위해서 heroku 에 관련된 커맨드라인인터페이스를 설치한다. (명령어를 처리하기 위한 도구)
https://toolbelt.heroku.com

heroku 버전 확인
```
heroku -v
```

#### heroku 배포와 git
- 로컬의 소스를 heroku 에 업로드 할 땐 git 을 이용한다.
- remote repository 에 업로드 되면 heroku 가 **자동으로** deploy 해준다.
- 오류가 나면 이때 확인 된다.

heroku 에 로그인해보자.
```
$ heroku login
```
아이디랑 패스워드 입력하면 로그인 성공.
```
Logged in as naan_ace@naver.com
```

**heroku 명령어**
- heroku apps 리스트 보기
```
$ heroku apps
```

- heroku app 만들기
```
$ heroku apps:create [앱이름]
$ heroku apps:create sooheroku

만약 $ heroku create 로만 앱을 만들면 mighty-river-50583 이라는 이름이 지어지게 된다.
```
https://sooheroku.herokuapp.com/ | https://git.heroku.com/sooheroku.git 이렇게 만들어지니 앱이름은 유니크하게 작성해야 한다.

- app 삭제
```
$ heroku apps:delete [앱이름]
```

node.js 에서 만든 어플리케이션을 heroku 에 등록할꺼다.

```
git init
git add .
git commit
git push
```
- .gitignore 파일은 업로드 하기 싫은 파일을 설정해둘 수 있다.
