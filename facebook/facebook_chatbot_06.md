
## 페이스북 메신저 플랫폼
- 페이스북 메신저 위에서 지원되는 서비스
- Send/Recv api 를 사용할 것임
- 유저의 메세지를 수신할 때 webhook 을 사용할 것임. (Callback 같은 개념)
- 템플릿 모양대로 메신저 보낼 수 있음
- 버튼 같은 UI 추가 가능
- 모든 정보를 자연어로 전달하지 않아도 된다.

## 봇 소개하기
- 페이스북 페이지에 직접 만든 봇을 링크할 수 있다.
- 사전에 등록되지 않은 사용자와 대화를 할 수 없도록 되어있다.
- 외부에 공개하려면 페이스북 사이트에서 심사 과정을 거쳐야 한다.

## FB Messenger API UPDATE
- 웹 페이지에 메신저를 넣을 수 있다. Customer Chat Plugin
- 여러명에게 동시에 메세지를 전송할 수 있다. Broadcast API
- 페이프북 페이지의 페이지 인사이트 탭의 정보를 가져올 수 있다. (동영상, 사진 ...)

## 챗봇 만들기

1) 페이스북에 로그인
2) [페이스북 개발자 페이지](https://developers.facebook.com)에서 개발자 등록 
3) 개발자 페이지 홈 > 내 앱 > 새 앱 추가
4) 앱의 대시보드 화면으로 간다.
5) 설정 > 기본 설정에 가서 '앱 아이디' 와 '앱 코드' 를 복사해둔다.
6) 메신저 product를 추가해보자. 대시보드 화면에 보이는 제품에서 'Messenger'를 찾고 '설정'을 클릭한다.
7) 토큰 생성 부분에 페이지를 선택하고 토큰을 생성한다.
페이지와 챗봇과 링크를 연결할 수 있게 창이 뜬다.
8) 생성된 페이지 액세스 코드를 복사해둔다.(다른 페이지로 이동하거나 시간이 지나면 코드가 바뀌는데, 바뀌어도 이전 코드를 사용 가능하다.)

> 페이스북에 앱을 만들어 놓은 상황.
> 챗봇이 운영되는 건 공개되는 곳에서 해야한다. 서버를 만들어야 하는데, heroku를 사용해보겠다.
> heroku 에 앱을 연결한다.
> firstmysoochatbot 이름의 주소를 만들었다.

9) webhooks를 설정해보자.
10) 콜백url 에 heroku주소를 넣는다.
11) 확인 토큰엔 확인하기 위한 글자를 넣는다. (--> 여기서 !! heroku 에서 먼저 작업해줘야함.)
12) 받아보기 필드는 어떤 이벤트에 반응할 것인가 하는 옵션이다. 일단 다 체크해두자.
[그림]
확인및 저장을 클릭
13) 완성하면 페이지를 선택하고 subscribe 버튼을 클릭한다.

---

작업 디렉토리 만들고 코드를 만들자.
bot의 page access token 과 app_secret 를 작성해둔다.

--- 

메신저 플랫폼 서버가 필요하다.
- HTTPS 프로토콜 사용 의무화

- mkdir moviechatbot
- cd moviechatbot
- npm init
- package.json 파일의 scripts start 추가
```
{
  "name": "moviechatbot",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node app.js"
  },
  "author": "",
  "license": "ISC"
}
```
- npm install express request body-parser mongoose -save
- dependencies 추가
```
"dependencies": {
  "body-parser": "^1.18.3",
  "express": "^4.16.3",
  "mongoose": "^5.1.5",
  "request": "^2.87.0"
}
```
- .gitignore 파일에 node_modules 폴더 추가 (파일이 너무 많거든. git 에 안올려도 heroku가 안에서 다운받아준다.)
.gitignore 파일 만들고 node_modules 작성해주면 됨.

- 시간절약을 위해 app.js파일 붙여넣기. 
- heroku 에 올려보자.
```
git init
git add .
git commit -m "Initial commit"
heroku login
heroku apps:create [App name]
heroku git:remote -a [App name]
git push heroku master
heroku open
```

암호 코드 자체 보안상 문제있겠지.
이걸 heroku 에서 변수로 설정해둘 수 있다.

헤로쿠 사이트 들어가서, 서버 선택하고 > Settings 클릭 > Config Vars> Reveal config Vars
여기서 변수 설정해둘 수 있다.
```
//app.js
access_token: process.env.PAGE_ACCESS_TOKEN
```
이렇게 변수 설정이 가능하다.
[그림]

Welcome page 만들기
facebook page 가서 > 설정 > 메시지 > 응답도우미 > Messenger 인사말 표시 on
curl 에서 weblcome page 작동 하는지 안하는지 체크해보려면?
```
curl -X POST -H "Content-Type: application/json" -d '{
  "setting_type":"call_to_actions",
  "thread_state":"new_thread",
  "call_to_actions":[
    {
      "payload":"Greeting"
    }
  ]
}' "https://graph.facebook.com/v2.6/me/thread_settings?access_token=[PAGE ACCESS TOKEN]"
```
PAGE ACCESS TOKEN 대신에 다른거 입력해야한다.
{"result":"Successfully added new_thread's CTAs"} 이런 결과가 나온다.

Callback 순서
메세지 수신 > app.js 의 processPostback > sendMessage() 실행 > 200 OK HTTPS

http 통신은 한번 요청할 때 마다 상태가 끝난다.
그래서 사용자 한 명당 정보를 저장하고 있을 저장소가 필요하다.

heroku 에서 세션저장이 가능하다.
서버 대시보드 > Resource > Add ons > mLab 을 돋보기에서 찾으면 mLab 몽고디비를 서비스하는 애드온이 보인다. > Provision 클릭
(에러가 났다면 [mlab.com](https://mlab.com)에서 회원가입하면 사용가능하다.)
몽고디비 config vars 이 자동으로 설정된 걸 볼 수 있다.

모델을 생성해보자.
models 폴더 생성
movie.js 파일 생성
```
var mongoose = require("mongoose");
var Schema = mongoose.Schema;

var MovieSchema = new Schema({
  user_id: {type: String},
  title: {type: String},
  plot: {type: String},
  date: {type: String},
  runtime: {type: String},
  director: {type: String},
  cast: {type: String},
  rating: {type: String},
  poster_url: {type: String}
});

module.exports = mongoose.model("Movie", MovieSchema);
```

데이터 접속 위한 모듈 사용 코드 추가 (코드에 없으면 추가하기)
```
var mongoose = require("mongoose");

var db = mongoose.connect(process.env.MONGODB_URI);
var Movie = require("./models/movie");
```

pdf 파일보면 processPostback, processMessage 를 구현해두었다.

메세지 유형에 따라 이벤트 지정해둔다.

자... 끝이래요...흠....

heroku logs --tail
하면 이때까지 주고받은 내용을 볼 수 있고
나중에 빅데이터에 활용할 수 있다.

---

한국어 자연어 처리 - 마음, 단비 

모든 영화관을 한번에 예약할 수 있는 사이트가 있었음 좋겠다...
