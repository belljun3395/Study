## Swagger

---

우선 swagger은 개발자가 REST 웹 서비스를 설계, 빌드, 문서화, 소비하는 일을 도와주는 대형 도구 생태계의 지원을 받는 오픈 소스 소프트웨어 프레임워크이다.

대부분의 사용자들은 스웨거 UI 도구를 통해 스웨거를 식별하며 스웨거 툴셋에는 자동화된 문서화, 코드 생성, 테스트 케이스 생성 지원이 포함된다.



### Swagger UI Express

---

이런 swagger를 express에서 사용하기 위한 npm이 swagger-ui-express이다.

이 npm은 swager.josn에 기반한 express API 문서에서 자동 생성되는 swagger-ui를 사용할 수 있도록 해준다.

그 결과 라우터를 통해 API 문서를 위한 서버를 개설할 수 있게 된다.



#### 사용

---



```js
const express = require('express');
const app = express();
const swaggerUi = require('swagger-ui-express');
const swaggerDocument = require('./swagger.json');

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument));
```

위와 같은 코드를 통해 http://<app_host>:<app_port>/api-docs 에서 문서를 볼 수 있다.



#### swagger-jsdoc

---

위의 예제에서 swaggerui의 옵션 설정을 swaggerUi.setup(swaggerDocument) 와 같은 방법을 통해 하였지만 swagger-jsdoc을 활용하면 더 간단하게 설정할 수 있다.



```js
// Initialize swagger-jsdoc -> returns validated swagger spec in json format
const swaggerSpec = swaggerJSDoc(options);

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec));
```



swagger-jsdoc에 대해 조금더 알아보자면 swagger-jsdoc은 JSDoc 애너테이션 코드를 읽고 OpenAPI 명세서를 만든다.



즉 아래와 같은 코드들이 있는 파일들을 

```js
/**
 * @openapi
 * /:
 *   get:
 *     description: Welcome to swagger-jsdoc!
 *     responses:
 *       200:
 *         description: Returns a mysterious string.
 */
app.get('/', (req, res) => {
  res.send('Hello World!');
});
```

다음과 같은 설정을 통해 읽어내는 것이다.

```js
const swaggerJsdoc = require('swagger-jsdoc');

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'Hello World',
      version: '1.0.0',
    },
  },
  apis: ['./src/routes/*.js'], // files containing annotations as above
};

const openapiSpecification = swaggerJsdoc(options);
```



다음은 본인이 구현해본 코드이다.

```js
// modules/swagger.js

const swaggerUi = require("swagger-ui-express")
const swaggereJsdoc = require("swagger-jsdoc")

const options = {
  swaggerDefinition: {
    info: {
      version: "1.0.0",
      title: "Swagger API TESt",
      description:
        "Test Swagger API",
    },
    servers: [
      {
        url: "http://localhost:3000", 
      },
    ], 
  },
  apis: ["./src/routes/swagger/*"], 
}
const specs = swaggereJsdoc(options)

module.exports = { swaggerUi, specs }
```

```js
// routes/swagger/user.js

/** 
*@swagger
*paths:
*  /users/login:
*    get:
*      tags:
*      - Login
*      summary: 로그인 페이지
*      responses:
*        "200":
*          description: 로그인페이지 로드 성공
*    post:
*      tags:
*      - Login
*      summary: 로그인 요청
*      requestBody:
*        content:
*          application/json:
*            schema:
*              $ref: '#components/schemas/User'
*              example : { "email" : "kjj3395", "password" : "1234" }
*        required: true
*      responses:
*        "201":
*          description: Created
*/
```

```js
// routes/swagger/user.js

/** 
*@swagger
* components:
*   schemas:
*     User:
*       type: object
*       properties:
*         email:
*           type: string
*           example: kjj3395
*         password:
*           type: string
*           example: "1234"
*       required:
*       - email
*       - password
*   securitySchemes: 
*    ApiKeyAuth: 
*      type: apiKey
*      name: api_key
*      in: header
*/
```



options라는 변수에 설정 내용들을 담에 swaggerJsdoc에 전달한 것을 볼 수 있다.

그리고 module.exports를 통해 app.js에서 다음과 같이 미들웨어 등록을 해주었다.

```js
const { swaggerUi, specs } = require('./src/modules/swagger');
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs));
```



그렇기에 주의할 점이 생긴다. 

options의 apis의 주소이다.

app.use 혹은 router.use 를 통해 미들웨어로 등록되어 주소의 기준도 app.js가 된다는 것을 명심하자!



#### api-key 활용

---

api-key는 배포를 할 경우 특정 사람만 api 를 사용할 수 있도록 제한하기 위해서 사용하는 것이다.

위처럼 swagger option과 components 를 통해서 사용할 인증 방법을 설정할 수 있다.

그러면 아래의 사진과 같이 authorize 버튼을 만들 수 있다.

![swagger_apikey](C:\Users\User\Desktop\Study\Study\Nodong\Npm\image\swagger_apikey.JPG)



이 버튼에서 특정 값을 입력하면 그 값은 swagger option과 components 를 통해 설정한 곳에 저장되게 된다.

```
*    ApiKeyAuth: 
*      type: apiKey
*      name: api_key
*      in: header
```

본인의 경우 다음과 같이 header에 api_key라는 이름으로 그 값을 저장했다.



그러면 이렇게 저장한 값을 가지고 api_key 가 맞는지 판단해야 한다.

```js
module.exports = {
    checkApiKey : function (req, res, next) {
        consoleHash('check APIKey :' + req.headers.api_key)
        
        const api_key = req.headers.api_key;
        if (api_key && api_key === "nanakim") { 
            next();
        } else {
            res.sendStatus(401);
        }
    }
}
```

위의 코드처럼 간단히 api_key가 맞는지 확인하는 함수를 만들면 된다.



이렇게 만든 함수는 아래와 같이 활용할 수 있다.

```js
router.post('/login', checkApiKey, verifyCookieToken, authenticate );
```



올바른 api_key 값이 들어오면 api 를 활용할 수 있고 그렇지 않으면 활용하지 못하게 된다.



### Swagger Editor 활용

---

Swagger Editor은 swagger 명세서를 작성하는 것을 도와주는 에디터이다.

이 Swagger Editor에 사용되는 문법은 아래의 내용과 같다.

그 문법을 바탕으로 명세서를 작성하면 JSON 혹은  YAML 형식으로 추출할 수 있다.

우리는 이렇게 추출한 명세서를 바탕으로 swagger-ui-express와 swagger-jsdoc을 작성할 수 있다.



다음 예제는 본인이 swagger 구현 연습을 하기 위해 작성한 명세서이다.

```yaml
---
1)
openapi: 3.0.0
info:
  title: Hairlog API
  description: API for Hairlog
  version: 1.0.0
servers:
- url: https://virtserver.swaggerhub.com/jon53/Hairlog/1.0.0
  description: SwaggerHub API Auto Mocking
- url: https://web-jongjun.herokuapp.com/
  description: Test Hairlog API at Local
security:
- ApiKeyAuth: []

2)
paths:
  /users/login:
    get:
      tags:
      - Login
      summary: 로그인 페이지
      responses:
        "200":
          description: 로그인페이지 로드 성공
    post:
      tags:
      - Login
      summary: 로그인 요청
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/User'
        required: true
      responses:
        "201":
          description: Created
3)          
components:
  schemas:
    User:
      required:
      - email
      - password
      type: object
      properties:
        email:
          type: string
          example: kjj3395
        password:
          type: string
          example: "1234"
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      name: X-API-KEY
      in: header

```



위의 명세서는 본인이 작성한 코드를 기반하면 크게 3부분으로 나누어진다.

1. swagger-jsdoc 옵션을 설정하기 위한 부분
2. 각각의 api에 대한 설정하기 위한 부분
3. 데이터나 보안에 관한 schema를 설정하기 위한 부분



위의 나누어진 내용을 각 부분에 적절한 형식에 맞추어 기록해주면 swagger을 사용할 준비를 마칠 수 있다.



### Basic Structure

----

OpenAPI 정의는 YAML 혹은 JSON의 형태로 작성할 수 있다. 

이 글에서는 아래와 같은 YAML 형식을 사용하여 예제를 작성할 것이다.



```yaml
openapi: 3.0.0
info:
  title: Sample API
  description: Optional multiline or single-line description in [CommonMark](http://commonmark.org/help/) or HTML.
  version: 0.1.9
servers:
  - url: http://api.example.com/v1
    description: Optional server description, e.g. Main (production) server
  - url: http://staging-api.example.com
    description: Optional server description, e.g. Internal staging server for testing
    
paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in CommonMark or HTML.
      responses:
        '200':    # status code
          description: A JSON array of user names
          content:
            application/json:
              schema: 
                type: array
                items: 
                  type: string
```



#### Metadata

----

모든 API 정의는 다음과 같은 OpenAPI 명세의 버전을 포함해야 한다.

```yaml
openapi: 3.0.0
```



info 부분은 title, description 그리고 version과 같은 API 의 정보를 담고 있다.

```yaml
info:
  title: Sample API
  description: Optional multiline or single-line description in [CommonMark](http://commonmark.org/help/) or HTML.
  version: 0.1.9
```



#### Servers

----

servers 부분은 API 서버 그리고 기본 URL을 명시한다.



```yaml
servers:
  - url: http://api.example.com/v1
    description: Optional server description, e.g. Main (production) server
  - url: http://staging-api.example.com
    description: Optional server description, e.g. Internal staging server for testing
```



그리고 이와 같은 Metadata와 Servers는 nodejs에서 `swagger-ui-express` 그리고 `swagger-jsdoc`  사용하여 다음과 같이 설정할 수 있다.

```js
const swaggerUi = require("swagger-ui-express")
const swaggereJsdoc = require("swagger-jsdoc")

const options = {
  swaggerDefinition: {
    openapi : "3.0.0",
    info: {
      version: "1.0.0",
      title: "Swagger API TESt",
      description:
        "Test Swagger API",
    },
    servers: [
      {
        url: "http://localhost:3000", 
      },
    ], 
  },
  apis: ["./src/routes/swagger/*"], 
}
const specs = swaggereJsdoc(options)

module.exports = { swaggerUi, specs }
```

이는 주로 modules라는 폴더를 만들이 그 속에 swagger.js 와 같이 저장하는 편이다.



#### Paths

---

paths 부분은 API의 각각의 도착지(endpoint) 그리고 그곳에서 사용하는 HTTP 메서드를 정의한다.



```yaml
paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in CommonMark or HTML
      responses:
        '200':
          description: A JSON array of user names
          content:
            application/json:
              schema: 
                type: array
                items: 
                  type: string
```



#### Parameters

---

API를 사용할 때 우리는 다음과 같은 URL path (`/users/{userId}`), query string (`/users?role=admin`), headers (`X-CustomHeader: Value`) or cookies (`Cookie: debug=0`) Parameter을 사용한다.

우리는 이러한 Parameter의 데이터 타입, 포멧, 그들이 필수인지 옵션인지 그리고 다른 세부사항을 설정하여 사용할 수 있다.



```yaml
paths:
  /users/{userId}:
    get:
      summary: Returns a user by ID.
      parameters:
        - name: userId // parameter 이름
          in: path // parameter 위치 
          required: true // parameter 필수여부
          description: Parameter description in CommonMark or HTML.
          schema:
            type : integer
            format: int64
            minimum: 1
      responses: 
        '200':
          description: OK
```



#### Request Body

---

API를 사용할 때 Rrequest Body를 보낸다면, `requestBody` 를 body 컨텐츠 그리고 미디어타입 묘사를 하는데 사용할 수 있다.



```yaml
paths:
  /users:
    post:
      summary: Creates a user.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                username:
                  type: string
      responses: 
        '201':
          description: Created
```



#### Responses

---

API 동작에서 우리는 `200 ok` 그리고 `404 not found` 와 같은 가능한 상태코드를 정의할 수 있다.

그리고 응답 body  schema 를 설정할 수 있다.

Schema는 인라인 또는 `$ref` 를 통한 참조로 정의될 수 있다.



```yaml
paths:
  /users/{userId}:
    get:
      summary: Returns a user by ID.
      parameters:
        - name: userId
          in: path
          required: true
          description: The ID of the user to return.
          schema:
            type: integer
            format: int64
            minimum: 1
      responses:
        '200':
          description: A user object.
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: integer
                    format: int64
                    example: 4
                  name:
                    type: string
                    example: Jessica Smith
        '400':
          description: The specified user ID is invalid (not a number).
        '404':
          description: A user with the specified ID was not found.
        default:
          description: Unexpected error
```



#### Input and Output Models

---

전역 `components/schemas` 부분은 API에서 사용되는 데이터 구조를 정의할 수 있도록 해준다.

그것들은 `$ref` 를 통해 schema가 필요한 곳이면 어디서든 참조될 수 있다.



예를들어 다음과 같은 JSON 객체가 있다고 생각해보자.

```yaml
{
  "id": 4,
  "name": "Arthur Dent"
}
```



이 객체의 데이터 구조는 다음과 같을 것이다.

```yaml
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
          example: 4
        name:
          type: string
          example: Arthur Dent
      # Both properties are required
      required:  
        - id
        - name
```



이렇게 정의한 데이터 구조는 다음과 같이 사용될 수 있다.

```yaml
paths:
  /users/{userId}:
    get:
      summary: Returns a user by ID.
      parameters:
        - in: path
          name: userId
          required: true
          schema:
            type: integer
            format: int64
            minimum: 1
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'    # <-------
  /users:
    post:
      summary: Creates a new user.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/User'      # <-------
      responses:
        '201':
          description: Created
```



#### Authentication

---

 `securitySchemes` 그리고 `security` 는 검증 메서드를 정의하는데 사용된다.



```yaml
components:
  securitySchemes:
    BasicAuth:
      type: http
      scheme: basic
security:
  - BasicAuth: []
```







<details>
<summary>참고</summary>
<a href=https://swagger.io/docs/specification/basic-structure/>https://swagger.io/docs/specification/basic-structure/</a></br>
</details>
