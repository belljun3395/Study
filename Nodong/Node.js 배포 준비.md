## Node.js 배포 준비

---

배포는 다른 사람이 서비스를 사용하게 되므로 개발 환경과 동일하게 진행할 수 없다.

서버도 죽지 않게 유지해야 하고, 에러 내역도 관리해야 한다.

또한, 각종 보안 위협에도 대처해야 한다.



### 서비스 운영을 위한 패키지

---



#### morgan

---

```js
if(process.env.NODE_ENV==='production'){
  app.use(morgan('combined'));
  app.use(helmet());
  app.use(hpp());
} else {
  app.use(morgan('dev'));
}
```

`process.env.NODE_ENV` 는 배포 환경인지 개발 환경인지를 판단할 수 있는 환경변수이다.

combined 모드는 dev 모드에 비해 더 많은 사용자 정보를 로그로 남긴다.



참고로 `process.env.NODE_ENV` 는 .env 에 넣을 수 없다.

.env 파일은 정적 파일이기 때문이다.

`NODE_ENV`  를 동적으로 바꾸는 방법은 cross-env 를 활용하는 것이다.



##### corss-env

---

cross-env 패키지를 사용하면 동적으로 process.env를 변경할 수 있다.

또한, 모든 운영체제에서 동일한 방법으로 변경할 수 있게 된다.



기존의 package.json을 다음과 같이 바꾼다.

```JSON
  "scripts": {
    "start": "cross-env NODE_ENV=production PORT=80 node app",
    "dev": "nodemon app"
  },
```

npm start는 배포 환경에서 사용하는 스크립트고, npm run dev는 개발 환경에서 사용하는 스크립트이다.



#### express-session

---

express-session의 경우 다음과 같이 설정해 준다.

```js
const sessionOption = {
  resave : false,
  saveUninitialized : false,
  secret : process.env.SESSION_SECRET,
  cookie : {
    httpOnly : true,
    secure : false,
  },
}

if(process.env.NODE_ENV==='production'){
  sessionOption.proxy=true;
  sessionOption.cookie.secure=true;
}
    
app.use(session(sessionOption));
```

배포 환경일 때는 proxy를 true로, cookie.secure을 ture로 바꾼다.

하지만 이는 무조건 적용해야 하는 것은 아니고, https를 적용할 경우에만 사용하면 된다.

proxy를 true로 적용해야 하는 경우는 https 적용을 위해 노드 서버 앞에 다른 서버를 두었을 때이다.

cookie.secure 또한 https 적용이나 로드밸런싱 등을 위해 true로 바꿔준다.



#### sequelize

---

sequelize에서 가장 큰 문제는 비밀번호가 하드코딩되어 있다는 것이다.

하지만 sequelize에서는 JSON 대신 JS 파일을 설정 파일로 쓸 수 있게 지원한다.



그래서 기존의 JSON 파일은 다음과 같은 JS 파일로 바꿀 수 있다.

```js
require('dotenv').config();

module.exports = {
  development : {
    username: "root",
    password: process.env.SEQUELIZE_PASSWORD,
    database: "app_practice",
    host: "127.0.0.1",
    dialect: "mysql",
    operatorsAliases:'false',
  },
  test : {
    username: "root",
    password: process.env.SEQUELIZE_PASSWORD,
    database: "database_test",
    host: "127.0.0.1",
    dialect: "mysql"
  },
  production: {
    username: "root",
    password: process.env.SEQUELIZE_PASSWORD,
    database: "app_practice",
    host: "127.0.0.1",
    dialect: "mysql",
    operatorsAliases:'false',
    logging: false,
  }
}

```

JS 파일이기 때문에 dotenv 모듈을 사용할 수 있다.



현재 쿼리를 수행할 때마다 콘솔에 SQL문이 노출된다.

배포 환경에서는 어떤 쿼리가 수행되는지 숨기는 것이 좋다.

따라서 production일 경우 logging에 false를 주어 쿼리 명령어를 숨겼다.



#### retire

---

retire은 설치된 패키지에 문제는 없는지 확인하는 패키지이다.

이를 위해 retire은 전역으로 설치해야 한다.



설치 후 콘솔에 retire 명령어를 입력하면 문제 있는 패키지가 있다면 콘솔에 내용이 출력된다.



#### npm audit

---

npm aduit은 retire과 유사한 명령이다.

게다가 npm audit fix를 입력하면 npm이 수정할 수 있는 오류는 자동으로 수정해 준다.



#### pm2

---

pm2는 원활한 서버 운영을 위한 패키지이다.

개발 시 nodemon을 쓴다면, 배포 시에는 pm2를 쓴다는 말이 있을 정도로 유용하다.

가장 큰 기능은 서버가 에러로 인해 꺼졌을 때 서버를 다시 켜주는 것이다.



또 하나 중요한 기능은 멀티 프로세싱이다.

멀티 스레딩은 아니지만 멀티 프로세싱을 지원하여 노드 프로세스 개수를 1개 이상으로 늘릴 수 있다.



하지만 단점도 있다.

멀티 스레딩이 아니므로 서버의 메모리 같은 자원을 공유하지는 못한다.

지금까지 세션을 메모리에 저장했는데, 메모리를 공유하지 못해서 프로세스 간에 세션이 공유되지 않은 것이다.

로그인 후 새로고침을 반복할 때 세션 메모리가 잇는 프로세스로 요청이 가면 로그인된 상태가 되고, 세션 메모리가 없는 프로세스로 요청이 가면 로그인이 안 된 상태가 되는 것이다.



이 문제는 Mencahced나 레디스와 같은 서비스를 사용하여 해결할 수 있다.



이제 다시 pm2로 돌아와 이를 설치해 보자.

이는 전역에 설치하고 package.json의 dependencies에도 추가해야 한다.



그리고 pakage.json을 다음과 같이 수정한다.

```json
  "scripts": {
    "start": "cross-env NODE_ENV=production PORT=80 pm2 start app.js",
    "dev": "nodemon app"
  },
```



이를 npm start를 통해 실행해 보면 기존과 다르게 백그라운드에서 돌아가는 것을 볼 수 있다.



백그라운드에서 돌고 있는 노드 프로세스를 확인하기 위해서는 다음과 같은 명령어를 사용하면 된다.

```
pm2 list
```



pm2 프로세스의 종료와 재시작을 위해서는 다음과 같은 명령어를 입력하면 된다.

```
pm2 kill
pm2 reload all
```



노드의 cluster 모듈처럼 클러스터링을 가능하게 하는 pm2의 클러스터링 모드는 `-i` 옵션을 통해 설정할 수 있다.



#### winston

---

실제 서버 운영 시 `console.log` 와 `console.error` 를 대체하기 위한 모듈이다.



 `console.log` 와 `console.error` 를 사용하면 개발 중에는 편리하게 서버의 상황을 파악할 수 있지만, 실제 배포 시에는 사용하기가 어렵다.

`console`  객체의 머서드들이 언제 호출되었는지 파악하기 힘들 뿐만 아니라 서버가 종료되는 순간 로그들도 사라져 버리기 때문이다.

에러가 발생하면 에러 메시지를 확인해야 하는데, 서버가 종료되어서 에러 메시지들이 날아가 버리는 황당한 일이 일어나게 된다.

이와 같은 상황을 방지하려면 로그를 파일이나 다른 데이터베이스에 저장해야 한다.

이때 winston을 사용한다.



```js
const { createLogger, format, transports } = require('winston');
const logger = createLogger({
    level : 'info',
    format : format.json(),
    transports : [
        new transports.File({filename:'combined.log'}),
        new transports.File({filename:'error.log',level:'error'}),
    ],
});
if(process.env.NODE_ENV!=='production'){
    logger.add(new transports.Console({format:format.simple()}));
}

module.exports = logger;
```

winston 패키지의 createLogger 메서드로 logger를 만든다.

인자로 logger에 대한 설정을 위와 같이 넣어준다.



이렇게 만든 logger 객체는 다른 파일에서 사용하면 된다.

info, warm, error 등의 메서드를 사용하면 해당 심각도가 적용된 로그가 기록된다.



```js
// catch 404 and forward to error handler  
app.use((req,res,next) => { 
  const err = new Error('NotFound');
  err.status=404;
  logger.info('hello');
  logger.error(err.message);
  next(err);
});
```

이는 app.js에서 사용하는 모습이다.



#### helmet, hpp

---

서버의 각종 취약점을 보완해주는 패키지들이다.

express 미들웨어로서 사용할 수 있다.

이 패키지를 사용한다고 해서 모든 취약점을 방어해주는 것은 아니므로 서버를 운영할 때는 주기적으로 취약점을 점검해야 한다.



#### connect-redis

---

멀티 프로세스 간 세션 공유를 위한 레디스와 익스프레스를 연결해주는 패키지이다.

기존에는 로그인 시 express-session의 세션 아이디와 실제 사용자의 정보가 메모리에 저장된다.

따라서 서버가 종료되어 메모리가 날아가면 접속자들의 로그인이 모두 풀려버린다.

이를 방지하기 위해서 세션 아이디와 실제 사용자 정보를 데이터베이스에 저장한다.

이때 사용하는 데이터베이스가 레디스이다.



레디스를 사용하려면 connect-redis 패키지뿐만 아니라 레디스 데이터베이스를 설치해야 한다.

서버에 직접 설치할 수도 있지만 레디스 호스팅을 해주는 서비스도 있다.

redislabs이다.

redislabs에서 connect-redis를 위한 DB를 만들고 Endpoint와 Redis Password를 복사해 .env에 붙여 넣는다.

이때 Endpoint에서 Host와 Port를 분리한다.



```js
const redisClient = redis.createClient({
  url: `redis://${process.env.REDIS_HOST}:${process.env.REDIS_PORT}`,
  password: process.env.REDIS_PASSWORD,
  legacyMode: true
});
redisClient.connect().catch(console.error)
```

.env에 저장한 정보를 바탕으로 위와 같이 redis와 연결한다.

이는 책의 코드와는 다르다.

그 이유는 레디스의 버전이 책은 v2or3이고 본인이 설치한 것은 v4이기 때문이다.



npm connect-redis를 보면 다음과 같은 설명이 있다.

```js
// redis@v4
const { createClient } = require("redis")
let redisClient = createClient({ legacyMode: true })
redisClient.connect().catch(console.error)

// redis@v3
const { createClient } = require("redis")
let redisClient = createClient()
```

v4는 이전과 다르게 `.connect()`를 수행해 주어야 하는 것이다.



#### nvm, n

---

노드 버전을 업데이트하기 위한 패키지이다.

