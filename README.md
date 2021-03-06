# Koa 기본 사용법

- Koa 애플리케이션은 미들웨어의 배열로 구성되어 있다.
- app.use 함수는 미들웨어 함수를 애플리케이션에 등록하는 역할
- 미들웨어 함수는 다음과 같은 구조를 가지고 있다.

```
(ctx, next) => {

}
```

- Koa의 미들웨어 함수는 ctx, next 두 개의 파라미터를 받는다.

  - ctx는 Context의 줄임말로 웹 요청과 응답에 관한 정보를 지니고 있다.
  - next는 현재 처리 중인 미들웨어의 다음 미들웨어를 호출하는 함수
  - 미들웨어를 등록하고 next 함수를 호출하지 않으면, 그 다음 미들웨어를 처리하지 않는다.
  - 만약 미들웨어에서 next를 사용하지 않으면 ctx => {} 와 같은 형태로 파라미터에 next를 설정하지 않아도 괜찮다.
    - 주로 다음 미들웨어를 처리할 필요가 없는 라우트 미들웨어를 나중에 설정할 때 이러한 구조로 next를 생략하여 미들웨어를 작성

- 미들웨어는 app.use를 사용하여 등록되는 순서대로 처리된다.

  <br>

## next 함수는 Promise를 반환

- next 함수를 호출하면 Promise를 반환한다. 이는 Koa가 Express와 차별화되는 부분
- next 함수가 반환하는 Promise는 다음에 처리해야 할 미들웨어가 끝나야 완료됨

  <br>

## async / await 사용하기

- Koa는 async / await를 정식으로 지원하기 때문에 해당 문법을 아주 편하게 사용할 수 있음

  <br>

## koa-router 사용하기

다음과 같이 사용한다.

```
const Koa = require("koa");
const Router = require("koa-router");

const app = new Koa();
const router = new Router();

// 라우터 설정
router.get("/", (ctx) => {
  ctx.body = "홈";
});
router.get("/about", (ctx) => {
  ctx.body = "소개";
});

// app 인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods());

app.listen(5000, () => {
  console.log("Listening to port 5000");
});

```

- router.get의 첫 번째 파라미터에는 라우트의 경로를 넣고, 두 번째 파라미터에는 해당 라우트에 적용할 미들웨어 함수를 넣는다.

- 여기서 get 키워드는 해당 라우트에서 사용할 HTTP 메서드를 의미하고, get 대신에 post,put,delete 등을 넣을 수 있다!

- Express와 유사한듯?

  <br>

## 라우트 파라미터와 쿼리

- 라우터의 파라미터를 설정할 때는 /about/:name 형식으로 콜론(:)을 사용하여 라우트 경로를 설정한다.
- 파라미터가 있을수도 있고 없을 수도 있다면 /about/:name? 같은 형식으로 파라미터 이름 뒤에 물음표를 사용한다.
- 이렇게 설정한 파라미터는 함수의 ctx.params 객체에서 조회 할 수 있다.

- URL 쿼리의 경우, 예를 들어 /posts/?id=10 같은 형식으로 요청했다면 해당 값을 ctx.query에서 조회할 수 있다. 쿼리 문자열을 자동으로 객체 형태로 파싱해 주므로 별도로 파싱 함수를 돌릴 필요가 없다. (문자열 형태의 쿼리 문자열을 조회해야 할 때는 ctx.querystring을 사용)

- 파라미터와 쿼리는 둘 다 주소를 통해 특정 값을 받아 올 때 사용하지만, 용도가 서로 조금씩 다르다.

  - 정해진 규칙은 없지만, 일반적으로 파라미터는 처리할 작업의 카테고리르 받아 오거나, 고유 ID 혹은 이름으로 특정 데이터를 조회할 때 사용
  - 쿼리는 옵션에 관련된 정보를 받아온다. 예를 들어 여러 항목을 리스팅하는 API라면, 어떤 조건을 만족하는 항목을 보여 줄지 또는 어떤 기준으로 정렬할지를 정해야 할 때 쿼리를 사용

  <br>

## REST API

- 웹 애플리케이션을 만들려면 데이터베이스에 정보를 입력하고 읽어 와야 한다. 그런데 웹 브라우저에서 데이터베이스에 직접 접속하여 데이터를 변경한다면 보안상 문제가 된다. 그래서 REST API를 만들어서 사용한다!

- 클라이언트가 서버에 자신이 데이터를 조회, 생성, 삭제, 업데이트하겠다고 요청하면, 서버는 필요한 로직에 따라 데이터베이스에 접근하여 작업을 처리한다.
- REST API는 요청 종류에 따라 다른 HTTP 메서드를 사용한다. HTTP 메서드는 여러 종류가있으며, 주로 사용하는 메서드는 다음과 같다.

  - GET : 데이터를 조회할 때 사용
  - POST : 데이터를 등록할 때 사용한다. 인증 작업을 거칠 때 사용하기도 한다.
  - DELETE : 데이터를 지울 때 사용한다.
  - PUT : 데이터를 새 정보로 통째로 교체할 때 사용한다.
  - PATCH : 데이터의 특정 필드를 수정할 때 사용한다.

- 메서드의 종류에 따라 get, post, delete, put, patch를 사용하여 라우터에서 각 메서드의 요청을 처리한다.

  - 라우트를 작성할때, route.get 이런식으로 작성했었는데, 여기서 get이 바로 HTTP 메서드 GET이다. POST 요청을 받고싶다면, route.post 이런식으로 입력

- REST API를 설계할 때는 API 주소와 메서드에 따라 어떤 역할을 하는지 쉽게 파악할 수 있도록 작성해야 한다.
  - 블로그 포스트용 REST API 예시
    종류|기능
    ---|---
    POST /posts|포스트 작성
    GET /posts|포스트 목록 조회
    GET /posts/:id|특정 포스트 조회
    DELETE /posts/:id|특정 포스트 삭제
    PATCH /posts/:id|특정 포스트 업데이트(구현 방식에 따라 PUT으로도 사용 가능)
    POST /posts/:id/comments|특정 포스트에 덧글 등록
    GET /posts/:id/comments|특정 포스트의 덧글 목록 조회
    DELETE /posts/:id/comments/:commentId|특정 포스트의 특정 덧글 삭제

## koa-bodyparser

- API 기능을 본격적으로 구현하기 전에 먼저 koa-bodyparser 미들웨어를 적용해야함.
- 이 미들웨어는 POST/PUT/PATCH 같은 메서드의 Request Body에 JSON 형식으로 데이터를 넣어주면, 이를 파싱하여 서버에서 사용할 수 있게 해줌!

  <br>

# MongoDB

## RDBMS

- 기존에는 MySQL, OracleDB, PostgreSQL 같은 RDMBS(관계형 데이터베이스)를 자주 사용했다. 그러나 관계형 데이터베이스에는 몇가지 한계가 있음!

  - 데이터 스키마가 고정적 : 새로 등록하는 데이터 형식이 기존에 있던 데이터들과 다르다면 기존 데이터를 모두 수정해야 새 데이터 등록 가능
  - 확장성 : RDBMS는 저장하고 처리해야 할 데이터양이 늘어나면 여러 컴퓨터에 분산시키는 것이 아니라, 해당 데이터베이스 서버의 성능을 업그레이드하는 방식으로 확장해야함

<br>

## MongoDB

- MongoDB는 이런 한계를 극복한 문서 지향적 NoSQL 데이터베이스
- MongoDB에 등록하는 데이터들은 유동적인 스키마를 지닐 수 있음
- 서버의 데이터양이 늘어나도 한 컴퓨터에서만 처리하는 것이 아니라 여러 컴퓨터로 분산하여 처리할 수 있도록 확장하기 쉽게 설계 되어 있음.
- MongoDB가 무조건 기존의 RDBMS보다 좋은것은 아니다.
  - 데이터의 구조가 자주 바뀐다면 MongoDB가 유리
  - 까다로운 조건으로 데이터를 필터링해야 하거나, ACID 특성을 지켜야한다면 RDBMS가 더 유리

<br>

## Mongoose

- Node.js 환경에서 사용하는 MongoDB 기반 ODM(Object Data Modelling) 라이브러리
- MongoDB 데이터베이스 문서들을 자바스크립트 객체처럼 사용할 수 있게 해준다.

<br>

## 데이터베이스의 스키마와 모델

- mongoose에는 스키마(schema)와 모델(model)이라는 개념이 있다.
  - 스키마(Schema) : 컬렉션에 들어가는 문서 내부의 각 필드가 어떤 형식으로 되어 있는지 정의하는 객체
  - 모델(Model) : 스키마를 사용하여 만드는 인스턴스, 데이터베이스에서 실제 작업을 처리할 수 있는 함수들을 지니고 있는 객체

### 모델 생성에 관해

```
const 변수명 = mongoose.model("스키마 이름", 스키마 객체)
```

- 모델은 model() 함수를 이용하여 생성하고, 파라미터는 기본적으로 두 개가 들어간다.
- 첫번째 파라미터는 스키마 이름이고, 두번째 파라미터는 스키마 객체이다.
- 여기서 설정한 스키마 이름은 그 이름의 복수형태로 데이터베이스에 컬렉션 이름을 만든다.
  - 예를들어 Post 로 만들었다면, 실제 데이터베이스에 만들어지는 컬렉션 이름은 posts이다.

<br>

## 페이지 기능을 구현할 때 참고

model.skip() 함수를 사용하는데, 이 함수는 넘긴다는 의미이다. 예를들어 skip함수에 10을 파라미터로 넣어주면, 처음 10개의 데이터는 넘기고 11부터 보여줄것이다. <br>
20이 파라미터로 들어간다면 처음 20개의 데이터는 스킵하고, 21부터 데이터를 보여줄것이다! <br>
따라서 한 페이지에 10개의 포스트만 보여주는 페이지 기능을 구현한다고 할 때 (page - 1) \* 10 을 skip 함수의 파라미터로 넣어준다! <br>
page 변수의 초깃값이 1이면, 0 \* 10 이므로 처음 10개의 데이터를 보여줄것이다. 이후는 위의 설명과 같다.

## 내용 길이 제한할 때 참고

- model.find()를 통해 조회한 데이터는 mongoose 문서 인스턴스의 형태이므로 데이터를 바로 변형 할 수 없다. 그 대신 toJSON() 함수를 실행하여 JSON 형태로 변환한 뒤 필요한 변형을 일으켜 주어야한다.
- lean() 함수를 이용하는 방법도있다. 이 함수를 사용하면 데이터를 처음부터 JSON형태로 조회할 수 있다. (따로 변환해서 처리 할 필요없음)

# JWT

## JWT?<br>

- JSON Web Token의 약자로, 데이터가 JSON으로 이루어져 있는 토큰을 의미함!
- 두 개체가 서로 안전하게 정보를 주고받을 수 있도록 웹 표준으로 정의된 기술

## 세션 기반 인증과 토큰 기반 인증의 차이

- 사용자의 로그인 상태를 서버에서 처리하는 데 사용할 수 있는 대표적인 두 가지 인증 방식이 있다.
  - 세션 기반 인증
  - 토큰 기반 인증

### 세션 기반 인증 시스템

세션 기반 인증 시스템의 과정 <br>

1. 사용자 로그인
2. 서버에서 사용자정보를 세션 저장소에 저장
3. 서버에서 사용자에게 세션 id를 발급
4. 사용자가 서버에 정보를 요청
5. 서버에서 세션을 조회
6. 서버에서 사용자에게 응답

세션 기반 인증 시스템에서 사용자가 로그인을 하면, 서버는 세션 저장소에 사용자의 정보를 조회하고 세션 id를 발급한다. 발급된 id는 주로 브라우저의 쿠키에 저장함!<br>
그 다음에 사용자가 다른 요청을 보낼 때마다 서버는 세션 저장소에서 세션을 조회한 후 로그인 여부를 결정하여 작업을 처리하고 응답을 한다.<br>
세션 저장소는 주로 메모리, 디스크, 데이터베이스 등을 사용한다.<br>
<br>
세션 기반 인증의 단점은 서버를 확장하기가 번거로워질 수 있다는 점이다.<br>
만약 서버의 인스턴스가 여러 개가 된다면, 모든 서버끼리 같은 세션을 공유해야 하므로 세션 전용 데이터베이스를 만들어야 할 뿐 아니라 신경 써야 할 것도 많다.<br>
그렇다고 해서 세션 기반 인증이 무조건 좋지 않은 것은 아니다. 잘 설계하면 충분히 좋은 시스템이 될 수 있다.<br>
<br>

### 토큰 기반 인증 시스템

토큰은 로그인 이후 서버가 만들어 주는 문자열을 말한다.<br>
해당 문자열 안에는 사용자의 로그인 정보가 들어 있고, 해당 정보가 서버에서 발급되었음을 증명하는 서명이 들어있다.<br>
서명 데이터는 해싱 알고리즘을 통해 만들어지는데, 주로 HMAC SHA256 혹은 RSA SHA256 알고리즘이 사용된다.<br>
<br>
토큰 기반 인증 시스템의 과정 <br>

1. 사용자 로그인
2. 서버에서 사용자에게 토큰 발급
3. 사용자는 토큰과 함께 서버에 요청
4. 서버에서 토큰 유효성 검사 후 사용자에게 응답

<br>
서버에서 만들어 준 토큰은 서명이 있기 때문에 무결성이 보장된다. 여기서 무결성이란 정보가 변경되거나 위조되지 않았으믈 의미하는 성질을 말한다.<br>
사용자가 로그인을 하면 서버에서 사용자에게 해당 사용자의 정보를 지니고 있는 토큰을 발급해 주고, 추후 사용자가 다른 API를 요청하게 될 때 발급받은 토큰과 함께 요청하게 된다.<br>
그러면 서버는 해당 토큰이 유효한지 검사하고, 결과에 따라 작업을 처리하고 응답한다.<br>
<br>
토큰 기반 인증 시스템의 장점은 서버에서 사용자 로그인 정보를 기억하기 위해 사용하는 리소스가 적다는 것이다. <br>
사용자 쪽에서 로그인 상태를 지닌 토큰을 가지고 있으므로 서버의 확장성이 매우 높다. <br>
서버의 인스턴스가 여러 개로 늘어나도 서버끼리 사용자의 로그인 상태를 공유하고 있을 필요가 없다!<br>
이 프로젝트는 토큰 기반 인증 시스템을 사용함! <br>

<br>

사용자가 브라우저에서 토큰을 사용할 때는 주로 두 가지 방법을 사용한다.<br>
첫 번째는 브라우저의 localStorage 혹은 sessionStorage에 담아서 사용하는 방법이고, 두 번째는 브라우저의 쿠키에 담아서 사용하는 방법이다.<br>
브라우저의 localStorage 혹은 sessionStorage에 토큰을 담으면 사용하기가 매우 편리하고 구현하기도 쉽다.<br>
하지만 만약 누군가가 페이지에 악성 스크립트를 삽입한다면 쉽게 토큰을 탈취할 수 있다.(XSS) <br>
<br>
쿠키에 담아도 같은 문제가 발생할 수 있지만 httpOnly라는 속성을 활성화하면 자바스크립트를 통해 쿠키를 조회할 수 없으므로 악성 스크립트로부터 안전하다. 그 대신 CSRF라는 공격에 취약해질 수 있다.<br>
이 공격은 토큰을 쿠키에 담으면 사용자가 서버로 요청을 할 때마다 무조건 토큰이 함께 전달되는 점을 이용해서 사용자가 모르게 원하지 않는 API 요청을 하게 만든다. <br>
예를 들어 사용자가 자신도 모르는 상황에서 어떠한 글을 작성하거나 삭제하거나, 또는 탈퇴하게 만들 수도 있다.<br>
<br>
단, CSRF는 CSRF 토큰 사용 및 Referer 검증 등의 방식으로 제대로 막을 수 있는 반면, XSS는 보안장치를 적용해 놓아도 개발자가 놓칠 수 있는 다양한 취약점을 통해 공격을 받을 수 있다.
<br>

- 토큰의 해석 결과는 다음과 같은 형태를 띈다.
  ```
  {
    _id: '614edf9264d56d91f061f2af',
    username: 'pjj186',
    iat: 1632561411,
    exp: 1633166211
  }
  ```
  iat 값은 이 토큰이 언제 만들어졌는지 알려 주는 값이고, exp값은 언제 만료되는지 알려주는 값이다.
