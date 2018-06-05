# Facebook API를 활용한 나만의 챗봇 만들기

기획 어떻게 하면 좋고 구축 어떻게 하면 좋은지에 대해 얘기할 것임.

## REST API

- **무엇을? 어떻게?** 에 대해 생각하기
- 개발하기 전, API 설계가 명확해야 한다..!!
- API 는 어디까지나 **인터페이스**. 
- 지극히 심플, 다양한 사람에게 공개할 것, 자원이 다른 쪽과 공유할 수 있는 걸 생각하기. (그게 API 거든)

## API 설계 가이드

- API 만들기 전에 서비스를 준비해놓고, (데이터)
- 어떤 정보를 필요로 하고 있는지? (블로그를 예를 들면 기사, 태그, 댓글, 카테고리...)
- 해당 자원에 어떤 작업이 필요한가? 리소스에 필요한 작업을 생각하기 (CRUD. 블로그 게시물 확인(R), 작성(C),...)
- 자원의 상하관계
- 게시물에는 코멘트를 갖고 있고, 게시물은 각종 카테고리에 속한다.
- Cool URI - 좋은 URI 를 만듭시다. (구글 다음 네이버의 로그인, 지도 서비스)
> - 쉽게 바뀌면 안된다.
> - 기존에 있는 걸 지원해준다.

## URL 설계
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

## HTTP 메소드 결정

REST API 에서 주로 사용하는 것은 4가지
- POST : 자원을 새로 만들 때 (create)
- GET : read
- PUT : update
- DELETE

## QR 코드 API 사용해보기

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

제공하고싶은 리소스 생각해보시고,
설계해볼께요.


