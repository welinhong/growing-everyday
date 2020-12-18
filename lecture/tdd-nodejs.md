# 테스트주도개발(TDD)로 만드는 NodeJS API 서버

인프런에서 김정환 강사님의 'TDD로 만드는 NodeJS API 서버 강의'를 듣고 정리한 내용입니다.

## 강의 목표
1. 노드로 API 서버를 만들 수 있다.
2. 테스트 주도 개발을 익힐 수 있다.

## 목차
[1. NodeJS 기초](#1-nodejs-기초)
- [NodeJS란](#nodejs란)
- [비동기 세계](#비동기-세계)

[2. 노드로 만나는 Hello World](#2-노드로-만나는-hello-world)  
- [Hello World 노드 버전](#hello-world-노드-버전)
- [라우팅 추가하기](#라우팅-추가하기)

[3. ExpressJS 기초](#3-expressjs-기초)   
- [익스프레스(Express) 소개](#익스프레스express-소개)
- [어플리케이션](#어플리케이션)
- [미들웨어](#미들웨어)
- [라우팅](#라우팅)
- [요청객체](#요청객체)
- [응답객체](#응답객체)
- [Hello world 익스프레스 버전](#hello-world-익스프레스-버전)


[4. REST API란](#4-rest-api란)  
- [HTTP Status Code](#http-status-code)

[5. 테스트 주도 개발 (TDD)](#5-테스트-주도-개발-tdd)  
- [테스트 주도 개발이란?](#테스트-주도-개발이란)
- [모카(mocha)](#모카mocha)
- [슈드(should)](#슈드should)
- [슈퍼테스트(superTest)](#슈퍼테스트supertest)

[6. TDD로 하는 API 서버 개발](#6-tdd로-하는-api-서버-개발)  
- [사용자 목록 조회 API](#사용자-목록-조회-api)
- [사용자 조회 API](#사용자-조회-api)
- [사용자 삭제 API](#사용자-삭제-api)
- [사용자 추가 API](#사용자-추가-api)
- [bodyParser 모듈](#bodyparser-모듈)
- [사용자 수정 API](#사용자-수정-api)

[7. 코드 리팩토링](#7-코드-리팩토링)  
- [라우터 클래스](#라우터-클래스)
- [컨트롤러 함수로 분리](#컨트롤러-함수로-분리)
- [테스트 환경 개선](#테스트-환경-개선)


[8. 데이터베이스](#8-데이터베이스)  
- [데이터베이스-소개](#데이터베이스-소개)
- [ORM 소개](#orm-소개)
- [노드의 ORM 시퀄라이져](#노드의-orm-시퀄라이져)
- [데이터베이스 - ORM 동기화](#데이터베이스---orm-동기화)
- [데이터베이스와 index 컨트롤러 연동](#데이터베이스와-index-컨트롤러-연동)
- [데이터베이스와 show 컨트롤러 연동](#데이터베이스와-show-컨트롤러-연동)
- [데이터베이스와 destroy 컨트롤러 연동](#데이터베이스와-destroy-컨트롤러-연동)
- [데이터베이스와 create 컨트롤러 연동](#데이터베이스와-create-컨트롤러-연동)
- [데이터베이스와 update 컨트롤러 연동](#데이터베이스와-update-컨트롤러-연동)

[☝️이번 강의에서 배운 것](#%EF%B8%8F이번-강의에서-배운-것)  

## 1. NodeJS 기초
### NodeJS란
- 브라우저 밖에서 자바스크립트 코드를 실행할 수 있다.
- 크롬에서 사용하는 V8 엔진을 사용한다.
    - V8 엔진: 자바스크립트 코드를 해석해 주는 해석기
- 이벤트 기반의 비동기 I/O 프레임 워크
![NodeJS Processing Model](/assets/image/Nodejs-Processing-Model.png)

*출처: https://www.researchgate.net/figure/Nodejs-Processing-Model_fig38_322896255*
    - 이벤트 기반: 클라이언트가 보내는 모든 요청을 이벤트로 처리한다.
    - 비동기 I/O: 무거운 잡들은 Non-blocking Worker(worker thread)에 전달해서 비동기로 처리한다.
- CommonJS를 구현한 모듈 시스템
    - **CommonJS는 파일형태로 모듈을 관리할 수 있게 한다.**
    - 웹 브라우저에서는 파일에 접근할 수 없었는데, NodeJS는 서버에서 돌아가는 코드이기 때문에 파일을 읽을 수 있다. 그래서 파일 하나하나를 모듈로 만들어 관리할 수 있다.
    - 모듈은 크게 기본 모듈, 써드파티 모듈, 사용자 정의 모듈로 나눌 수 있다.
      - 사용자 정의 모듈: `module.exports = {}` 로 직접 모듈을 만들어 사용할 수 있다.

### 비동기 세계

- 노드는 기본적으로 비동기로 동작한다. 파일을 읽을 때에도 비동기로, 네트워크 통신할 때도 비동기로 한다.
- File System

    ```jsx
    const fs = require('fs')

    // 1.
    const data = fs.readFileSync('./data.txt', 'utf8')
    console.log(data) // utf8을 넣지 않으면 Buffer값을 출력한다.

    // 2.
    const data = fs.readFile('./data.txt', 'utf8', function(err, data) {
    	console.log(data)
    })
    ```

## 2. 노드로 만나는 Hello World

### Hello World 노드 버전

노드의 헬로월드 코드 

```jsx
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

// listen(): 서버가 클라이언트의 요청을 받을 수 있게 종료하지 않고 계속 대기하도록 하는 함수 (요청대기상태)
server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
})
```

### 라우팅 추가하기
<!-- TODO: 라우팅 추가하는 부분 강의보고 정리하기 -->


**http 요청을 보낼 수 있는 커맨드라인 명령어, curl**
`curl -X GET 'localhost:3000'`
- `-v` 를 붙이면 http response 상세한 데이터를 받아볼 수 있다. (status code, cotent-type 같은 데이터들)

    `curl -X GET 'localhost:3000/users' -v`


## 3. ExpressJS 기초
ExpressJS 프레임웍을 사용하는 이유는?
중복 코드를 제거하고, 각 route 별 분기를 좀 더 쉽게 하기 위해 ExpressJS 모듈을 사용한다!

### 익스프레스(Express) 소개

- NodeJS로 만들어진 웹 프레임워크
- [http://expressjs.com/en/starter/installing.html](http://expressjs.com/en/starter/installing.html)
<!-- TODO: getting started 문서 읽기 -->

### 어플리케이션

- 익스프레스 인스턴스를 어플리케이션이라 한다. `const app = express()`
- 서버에 필요한 기능인 미들웨어를 어플리케이션에 추가해서 사용한다.
- 어플리케이션에 라우팅 설정을 할 수 있다.

### 미들웨어

- 미들 웨어를 추가할 때에는 `use`를 사용한다. ex) `app.use(logger)`
- `next()`를 호출하지 않으면 다음 코드가 실행이 되지 않는다.
- 미들웨어는 일반 미들웨어와 에러 미들웨어로 나눠져 있다.
  - 일반 미들웨어
      - 인자로 req, res, next을 받는다.
  - 에러 미들웨어
      - 이전 미들웨어에서 전달한 에러를 처리하거나 다음 미들웨어에 에러를 전달한다.
      - 인자로 err, req, res, next을 받는다.

  ```jsx
  function commonmw(req, res, next) {
  	console.log('commonmv')
  	next(new Error('error occured'))
  }
  function errormw(err, req, res, next) {
  	console.log('errormw')
  	next()
  }
  ```
- 서버쪽에 로그를 남겨주는 middleware - morgan
![morgan log](/assets/image/morgan-log.png)

### 라우팅

- 요청 url에 대해 적절한 핸들러 함수을 연결해 주는 기능을 라우팅이라고 한다.
- 어플리케이션의 get(), post() 메소드로 구현할 수 있다.
- 라우팅을 위한 전용 Router 클래스를 사용할 수도 있다.
    - express router

### 요청객체

- 클라이언트 요청 정보를 담은 객체를 요청(request) 객체라고 한다.
- http 모듈의 request 객체를 래핑한 것이다.
    - request로 부터 요청 정보들을 쉽게 빼낼 수 있다.
- req.params(), req.query(), req.body() 메소드를 주로 사용한다.
  - req.params
      - This property is an object containing properties mapped to **the named route "parameters"**.
      - ex) route /user/:name, "name" property is available as req.params.name
  - req.query
      - This property is an object containing a property for each **query string parameter in the route**.

### 응답객체

- 클라이언트 응답 정보를 담은 객체를 요청(response) 객체라고 한다.
- http 모듈의 response 객체를 래핑한 것이다.
- res.send(), res.status(), res.json() 메소드를 주로 사용한다.

### Hello world 익스프레스 버전

```jsx
var express = require('express');
var app = express();

app.get('/', function (req, res) {
  res.send('Hello World!');
});
// 여기서 req, res는 http의 req, res가 아니라
// express의 req, res 이다.
// 그래서 res.send를 사용할 수 있었다.

app.listen(3000, function () {
  console.log('Example app listening on port 3000!');
});
```

## 4. REST API란

경로에는 웬만하면 명사만 사용한다.
CRUD는 익스프레스 어플리케이션의 메소드로 구현되어 있다.

### HTTP Status Code

1XX: 아직 처리중

2XX: 자, 여기있어

- 200: 성공(success), GET, PUT
- 201: 작성됨(created), POST
- 204: 내용 없음 (No Conent), DELETE

3XX: 잘 가~

4XX: 니가 문제임 (클라가 문제)

- 400: 잘못된 요청 (Bad Request)
- 401: 권한 없음 (Unauthorized)
- 404: 찾을 수 없음 (Not found)
- 409: 충돌 (Conflict), 생성할 때 이미 있는 자원인데 또 생성하려고 하면 발생한다.

5XX: 내가 문제임

- 500: 서버 에러 (Interel server error)

## 5. 테스트 주도 개발 (TDD)

### 테스트 주도 개발이란?
<!-- TODO: TDD에 대해 정의하기 -->
- TDD로 개발하자!
- mocha, should, superTest
- 처음 개발시에는 시간이 걸리지만, 유지 보수 시간을 많이 줄여준다.

### 모카(mocha)
- 테스트 코드를 돌려주는 테스트 러너
  - 노드가 테스트 코드를 돌려주진 않는다. 테스트 코드를 돌려주는 건 모카!
- 테스트 코드 파일명을 `*.spec.js`로 짓는다. (spec = '명세서'라 생각하면 된다.)
- 테스트 수트: 테스트 환경, `describe()`로 구현
- 테스트 케이스: 실제 테스트, `it()`로 구현
- https://mochajs.org

### 슈드(should)
- 노드 assert 말고 서드파티 라이브러리를 사용하라.
- should는 assert 라이브러리이다.
- 테스트 코드의 가독성을 높일 수 있다.  `result.should.be.equal('Hello')`
- [https://github.com/tj/should.js/](https://github.com/tj/should.js/)

여기까지는 함수의 기능을 테스트 하는 단위 테스트이다.

### 슈퍼테스트(superTest)
- 슈퍼 테스트는 익스프레스 통합 테스트용 라이브러리다. (API 기능 테스트)
- 내부적으로 익스프레스 서버를 구동시켜 실제 요청을 보낸 뒤 결과를 검증한다.
- [https://github.com/visionmedia/supertest](https://github.com/visionmedia/supertest)


```jsx
describe('GET /users는', () => {
  it('...', (done) => {
    request(app)
      .get('/users')
      .end((err, res) => {
        console.log(res.body)
        done() // 비동기 테스트일 때 done 함수가 필요하다.
      })
  })
})
```

## 6. TDD로 하는 API 서버 개발
**API 서버 개발하기 전 `Rule & Tip`**

- import문 순서: 보통 npm 모듈은 위에 쓰고, 직접 만든 모듈은 아래에 쓴다.
- `node_modules/.bin/mocha index.spec.js` 와 package.json script 에서의 `mocha index.spec.js` 는 같다.
- query는 **string**으로 들어온다.
  
### 사용자 목록 조회 API

성공

- 유저 객체를 담은 배열로 응답한다.
- 최대 limit 갯수만큼 응답한다.

실패

- limit이 숫자형이 아니면 400을 응답한다.
- offset이 숫자형이 아니면 400을 응답한다. (앞에 스킵되는 데이터 갯수)


코드

  index.spec.js

  ```jsx
  const request = require('supertest')
  const should = require('should')
  const app = require('./index')

  describe('GET /users는', () => {
    describe('성공시', () => {
      it('유저 객체를 담은 배열로 응답한다', (done) => {
        request(app)
          .get('/users')
          .end((err, res) => {
            res.body.should.be.instanceOf(Array)
            done()
          })
      })

      it('최대 limit 갯수만큼 응답한다.', (done) => {
        request(app)
          .get('/users?limit=2')
          .end((err, res) => {
            res.body.should.be.lengthOf(2)
            done()
          })
      })
    })

    describe('실패시', () => {
      it('limit이 숫자형이 아니면 400을 응답한다.', (done) => {
        request(app)
          .get('/users?limit=two')
          .expect(400)
          .end(done)
      })
    })
  })
  ```

  index.js

  ```jsx
  app.get('/users', (req, res) => {
    req.query.limit = req.query.limit || 10
    const limit = parseInt(req.query.limit) // query는 string으로 들어온다.
    if (Number.isNaN(limit)) {
      return res.status(400).end()
    }
    res.json(users.slice(0, limit))
  })
  ```

### 사용자 조회 API

GET /users/:id

성공

- id가 1인 유저 객체를 반환한다.

실패

- id가 숫자가 아닐 경우 400으로 응답한다.
- id로 유저를 찾을 수 없을 경우 404로 응답한다.

코드

index.spec.js

```jsx
describe('GET /users/1는', () => {
  describe('성공시', () => {
    it('id가 1인 유저 객체를 반환한다.', (done) => {
      request(app)
        .get('/users/1')
        .end((err, res) => {
          res.body.should.have.property('id', 1)
          done()
        })
    })
  })

  describe('실패시', () => {
    it('id가 숫자가 아닐 경우 400으로 응답한다.', (done) => {
      request(app)
        .get('/users/one')
        .expect(400)
        .end(done)
    })

    it('id로 유저를 찾을 수 없을 경우 404로 응답한다.', (done) => {
      request(app)
        .get('/users/100')
        .expect(400)
        .end(done)
    })
  })
})
```

index.js

```jsx
app.get('/users/:id', (req, res) => {
  const id = parseInt(req.params.id, 10)
  if (Number.isNaN(id)) {
    return res.status(400).end()
  }

  const user = users.find((user) => user.id === id)
  if (!user) {
    return res.status(400).end()
  }
  res.json(user)
})
```

### 사용자 삭제 API

DELETE /users/:id

성공

- 204를 응답한다.

실패

- id가 숫자가 아닐 경우 400으로 응답한다.

코드

index.spec.js

```jsx
describe('DELETE /users/1는', () => {
  describe('성공시', () => {
    it('204를 응답한다.', (done) => {
      request(app)
        .delete('/users/1')
        .expect(204)
        .end(done)
    })
  })

  describe('실패시', () => {
    it('id가 숫자가 아닐 경우 400으로 응답한다.', (done) => {
      request(app)
        .delete('/users/one')
        .expect(400)
        .end(done)
    })
  })
})
```

index.js

```jsx
app.delete('/users/:id', (req, res) => {
  const id = parseInt(req.params.id, 10)
  if (Number.isNaN(id)) {
    return res.status(400).end()
  }
  users = users.filter((user) => user.id !== id)
  res.status(204).end()
})
```

### 사용자 추가 API

POST /users

성공

- 201 상태코드를 반환한다.
- 생성된 유저 객체를 반환한다.
- 입력한 name을 반환한다.

실패

- name 파라미터 누락시 400을 반환한다.
- name이 중복일 경우 409를 반환한다.

before
- 테스트 케이스가 동작하기 전에 미리 실행되는 함수로, 두 케이스 이상에 동일한 코드가 있는 경우 before을 사용한다.

- index.spec.js
  ```jsx
  describe('실패시', () => {
    it('name 파라미터 누락시 400을 반환한다.', (done) => {
      request(app)
        .post('/users')
        .send({})
        .expect(400)
        .end(done)
    }),
    it(('name이 중복일 경우 409를 반환한다'), (done) => {
      request(app)
        .post('/users')
        .send({name: 'daniel'})
        .expect(409)
        .end(done)
    })
  })
  ```

- index.js
  ```jsx
  app.use(express.json()) // for parsing application/json
  app.use(express.urlencoded({ extended: true })) // for parsing application/x-www-form-urlencoded

  app.post('users', (req, res) => {
    const name = req.body.name
    if (!name) return res.status(400).end()

    const isConflict = users.filter((user) => user.name === name).length
    if (isConflict) return res.status(409).end()

    const id = Date.now()
    const user = { id, name }
    users.push(user)
    res.status(201).json(user)
  })
  ```


### bodyParser 모듈

- req.body.name 으로 body에 접근할 수 없다. ("express에서는 body에 접근하는 메소드를 지원하고 있지 않다."고 강의에서 얘기했지만, 현재 express에서는 관련 메소드를 지원한다!)
- [강의] body-parsing middleware: `bodyParser`, `multer`
    - multer은 이미지 같은 큰 데이터에 사용한다.
- [최신 문서] body-parsing middleware: [express.json()](https://expressjs.com/en/4x/api.html#express.json) or [express.urlencoded()](https://expressjs.com/en/4x/api.html#express.urlencoded).
    - `express.json`: parsing application/json
    - `express.urlencoded`: parsing application/x-www-form-urlencoded
    - `req.body` (20.09.13 기준)
        Contains key-value pairs of data submitted in the request body. By default, it is `undefined`, and is populated when you use body-parsing middleware such as [express.json()](https://expressjs.com/en/4x/api.html#express.json) or [express.urlencoded()](https://expressjs.com/en/4x/api.html#express.urlencoded).

        The following example shows how to use body-parsing middleware to populate `req.body`.

        ```jsx
        var express = require('express')

        var app = express()

        app.use(express.json()) // for parsing application/json
        app.use(express.urlencoded({ extended: true })) // for parsing application/x-www-form-urlencoded

        app.post('/profile', function (req, res, next) {
          console.log(req.body)
          res.json(req.body)
        })
        ```

### 사용자 수정 API

PUT /users/:id

success

- 변경된 name을 응답한다.

error

- 정수가 아닌 id일 경우 400 응답
- name이 없을 경우 400 응답
- 없는 유저일 경우 404 응답
- 이름이 중복일 경우 409 응답

index.spec.js

```jsx
describe('PUT /users/:id는', () => {
  describe('성공시', () => {
    it('변경된 name을 응답한다.', (done) => {
      request(app)
        .put('/users/2')
        .send({ name: 'welin' })
        .end((err, res) => {
          res.body.should.have.property('name', 'welin')
          done()
        })
    })
  })
  describe('실패시', () => {
    it('정수가 아닌 id일 경우 400 응답', (done) => {
      request(app)
        .put('/users/two')
        .expect(400)
        .end(done)
    })
    it('name이 없을 경우 400 응답', (done) => {
      request(app)
        .put('/users/2')
        .send({})
        .expect(400)
        .end(done)
    })
    it('없는 유저일 경우 404 응답', (done) => {
      request(app)
        .put('/users/100')
        .send({ name: 'welinhong' })
        .expect(404)
        .end(done)
    })
    it('이름이 중복일 경우 409 응답', (done) => {
      request(app)
        .put('/users/2')
        .send({ name: 'chris' })
        .expect(409)
        .end(done)
    })
  })
})
```

index.js

```jsx
app.put('/users/:id', (req, res) => {
  const id = parseInt(req.params.id, 10)
  if (Number.isNaN(id)) {
    return res.status(400).end()
  }
  const name = req.body.name
  if (!name) {
    return res.status(400).end()
  }
  const isSameName = users.find((user) => user.name === name)
  if (isSameName) {
    return res.status(409).end()
  }
  const user = users.find((user) => user.id === id)
  if (!user) {
    return res.status(404).end()
  }
  user.name = name

  res.json(user)
})
```


## 7. 코드 리팩토링

### 라우터 클래스

`user/index.js` 

- 라우트에 대한 컨트롤러 함수를 바인딩 한다.
- 코드

    ```jsx
    const express = require('express')
    const router = express.Router()
    const ctrl = require('./user.ctrl')

    router.get('/', ctrl.index)
    router.get('/:id', ctrl.show)
    router.delete('/:id', ctrl.destroy)
    router.post('/', ctrl.create)
    router.put('/:id', ctrl.update)

    module.exports = router
    ```

### 컨트롤러 함수로 분리

`user/index.ctrl.js`

- 컨트롤러 로직에 대해서만 정의를 한다. (API 로직)
- 코드

    ```jsx
    const models = require('../../models')

    const index = (req, res) => {
      req.query.limit = req.query.limit || 10
      const limit = parseInt(req.query.limit) // query는 string으로 들어온다.
      if (Number.isNaN(limit)) {
        return res.status(400).end()
      }

      models.User.findAll({})
        .then(users => {
          res.json(users)
        })
    }

    const show = (req, res) => {
      const id = parseInt(req.params.id, 10)
      if (Number.isNaN(id)) {
        return res.status(400).end()
      }

      models.User.findOne({
        where: { id }
      }).then((user) => {
        if (!user) return res.status(400).end()
        res.json(user)
      })
    }

    const destroy = (req, res) => {
      const id = parseInt(req.params.id, 10)
      if (Number.isNaN(id)) {
        return res.status(400).end()
      }

      models.User.destroy({
        where: { id }
      }).then(() => {
        res.status(204).end()
      })
    }

    const create = (req, res) => {
      const name = req.body.name
      if (!name) {
        return res.status(400).end()
      }

      models.User.create({ name })
        .then((user) => {
          res.status(201).json(user)
        })
        .catch((err) => {
          // SequelizeUniqueConstraintError - DBMS, sqlist가 보내주는 에러 메시지
          if (err.name === 'SequelizeUniqueConstraintError') {
            return res.status(409).end()
          }
          res.status(500).end()
        })
    }

    const update = (req, res) => {
      const id = parseInt(req.params.id, 10)
      if (Number.isNaN(id)) return res.status(400).end()

      const name = req.body.name
      if (!name) return res.status(400).end()

      models.User.findOne({
        where: { id }
      }).then((user) => {
        if (!user) return res.status(404).end()
        user.name = name
        user.save()
          .then(() => res.json(user))
          .catch((err) => {
            // 중복 체크는 model 정의에서 한다. 모델에서 뱉은 에러를 출력해준다.
            if (err.name === 'SequelizeUniqueConstraintError') {
              return res.status(409).end()
            }
            res.status(500).end()
          })
      })
    }

    module.exports = {
      index,
      show,
      destroy,
      create,
      update
    }
    ```

### 테스트 환경 개선

- npm script에 환경변수를 주입해서, 개발 환경일때만 로그가 찍히도록 한다.

    ```jsx
    // package.json
    "scripts": {
        "test": "NODE_ENV=test mocha api/user/user.spec.js",
        "start": "node bin/www.js"
    },

    // index.js
    if (process.env.NODE_ENV !== 'test') {
      app.use(morgan('dev'))
    }
    ```

- `bin/www.js` 파일을 생성
    - 서버 구동을 따로 관리한다.

    ```jsx
    const app = require('../index')

    app.listen(3000, () => {
      console.log('Server is running on 3000 port')
    })
    ```

## 8. 데이터베이스

### 데이터베이스 소개

- SQL
    - 테이블 형식의 데이터 베이스, 컬럼/로우가 있다.
    - MySQL, PostgreSQL, Aurora, Sqlite (파일형태의 데이터베이스)
- NoSQL
    - document 형식의 데이터베이스, json 형식의 데이터베이스.
    - MongoDB, DynamoDB
- In Memory DB
    - 메모리 안에 데이터베이스를 만들어 놓은 것.디비가 재구동되면 데이터가 날라가게 된다. 인증토큰, 세션 같은 것을 넣는데 사용한다.
    - Redis, Memcashed

### ORM 소개

- 데이터베이스를 객체로 추상화해 논 것을 ORM(Object Relational Mapping)이라고 한다.
- 쿼리를 직접 작성하는 대신 ORM의 메소드로 데이터를 관리할 수 있는 것이 장점이다.
- 노드에서 SQL ORM은 시퀄라이져(Sequelize)가 있다. 노드에서 가장 많이 쓰이는 ORM 툴이다.

### 노드의 ORM 시퀄라이져

```sql
insert users ('name') values ('alice')
-> User.create({name: 'alice'})

select * from users
-> User.findAll()

update users set name = 'bek' where id = 1
-> User.update({name: 'bek'}, {where: {id: 1}})

delete from users where id = 1
-> User.destroy({where: { id: 1 })
```

- 위의 예시처럼 쿼리를 직접 작성하는 대신 ORM의 메소드로 데이터를 관리할 수 있다.

### 모델 정의

- 데이터베이스 테이블을 ORM으로 추상화한 것을 모델이라고 한다.
- 우리가 사용할 유저 모델을 만들어 보자.
    - `sequelize.define()`: 모델 정의
    - `sequelize.sync()`: 데이터베이스 연동

### 데이터베이스 - ORM 동기화

`bin/sync-db.js` 파일을 생성한다.

- 데이터베이스를 싱크하는 역할을 하는 모듈

```jsx
const models = require('../models')

module.exports = () => {
  // models.sequelize.sync -> 내부적으로 프로미스를 리턴한다.
  return models.sequelize.sync({ force: true })
}
```

`bin/www.js` 서버를 실행하기 전, syncDB를 실행한다.

- 서버를 돌려주는 친구

```jsx
const app = require('../index')
const syncDB = require('./sync-db')

syncDB().then(() => {
  app.listen(3000, () => {
    console.log('Server is running on 3000 port')
  })
})
```

### 데이터베이스와 index 컨트롤러 연동

- API 로직인 `user.ctrl.js`에서 모델을 연동하여 디비를 연결한다.
- 각각의 it, describe를 단독 테스트하고 싶을 때, `only` 를 사용한다.
    - `describe.only('GET /users는', () => {}`
    - `it.only('유저 객체를 담은 배열로 응답한다', (done) => {}`

```jsx
const models = require('../../models')

const index = (req, res) => {
  req.query.limit = req.query.limit || 10
  const limit = parseInt(req.query.limit) // query는 string으로 들어온다.
  if (Number.isNaN(limit)) {
    return res.status(400).end()
  }

  models.User.findAll({})
    .then(users => {
      res.json(users)
    })
}
```

### 데이터베이스와 show 컨트롤러 연동

- mocha의 `-w` 옵션 ⇒ watch!
    - "test": "NODE_ENV=test mocha api/user/user.spec.js -w"

```jsx
const show = (req, res) => {
  const id = parseInt(req.params.id, 10)
  if (Number.isNaN(id)) {
    return res.status(400).end()
  }

  models.User.findOne({
    where: { id }
  }).then((user) => {
    if (!user) return res.status(400).end()
    res.json(user)
  })
}
```

### 데이터베이스와 destroy 컨트롤러 연동

```jsx
const destroy = (req, res) => {
  const id = parseInt(req.params.id, 10)
  if (Number.isNaN(id)) {
    return res.status(400).end()
  }

  models.User.destroy({
    where: { id }
  }).then(() => {
    res.status(204).end()
  })
}
```

### 데이터베이스와 create 컨트롤러 연동

- 중복 이름 체크는 ORM model에서 `unique: true` 설정을 해준다.

    ```jsx
    const User = sequelize.define('User', {
      name: {
        type: Sequelize.STRING,
        unique: true
      }
    })
    ```

```jsx
const create = (req, res) => {
  const name = req.body.name
  if (!name) {
    return res.status(400).end()
  }

  models.User.create({ name })
    .then((user) => {
      res.status(201).json(user)
    })
    .catch((err) => {
      // SequelizeUniqueConstraintError - DBMS, sqlist가 보내주는 에러 메시지
      if (err.name === 'SequelizeUniqueConstraintError') {
        return res.status(409).end()
      }
      res.status(500).end()
    })
}
```

### 데이터베이스와 update 컨트롤러 연동

```jsx
const update = (req, res) => {
  const id = parseInt(req.params.id, 10)
  if (Number.isNaN(id)) return res.status(400).end()

  const name = req.body.name
  if (!name) return res.status(400).end()

  models.User.findOne({
    where: { id }
  }).then((user) => {
    if (!user) return res.status(404).end()

    user.name = name
    user.save()
      .then(() => res.json(user))
      .catch((err) => {
        // 중복 체크는 model 정의에서 한다. 모델에서 뱉은 에러를 출력해준다.
        if (err.name === 'SequelizeUniqueConstraintError') {
          return res.status(409).end()
        }
        res.status(500).end()
      })
  })
}
```

## 끗

- 환경에 따라 db file을 초기화 한다.

```jsx
const models = require('../models')

module.exports = () => {
  // models.sequelize.sync -> 내부적으로 프로미스를 리턴한다.
  const options = {
    force: process.env.NODE_ENV === 'test' ? true : false
  }
  return models.sequelize.sync(options)
}
```

# ☝️이번 강의에서 배운 것

- **테스트 코드를 먼저 만들고, 비즈니스 로직을 만들어야 한다.**
- 테스트 주도 개발이란 무엇인지에 대해 알 수 있었다.
    - TDD의 장점은 리팩토링에 대한 사이드 이펙트를 줄일 수 있고, 개발 전 개발해야 하는 피쳐를 명확히 정리할 수 있다는 것이다. 무엇보다 **리팩토링에 대한 부담을 많이 줄여 준다는 점이 가장 큰 장점**이다. 테스트 코드가 사이드이팩트를 잡아주니 **언제든 쉽게 리팩토링 할 수 있다!**
- 테스트 주도 개발 코드를 짤 수 있었다.
    1. API **성공**, **실패** 케이스를 리스트업 한다.
    2. 성공 실패 케이스를 각각 descrube로 묶고, 각 테스트 케이스를 it으로 구현한다.
- 파일을 기능에 따라 분리하는 방법을 배웠다.
    - ctrl, spec, index
    - db
    - bin 명령어
- curl에 대해서 알 수 있었다.
- sqlite을 알 수 있었다.
