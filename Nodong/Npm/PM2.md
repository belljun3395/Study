## PM2

---

우선 Node.js는 기본적으로 싱글 스레드이다.

Node.js 애플리케이션은 단일 CPU 코어에서 실행되기 때문에 CPU의 멀티코어 시스템을 사용할 수 없다.

Node.js는 이러한 문제를 해결하기 위해 클러스터 모듈을 통해 단일 프로세스를 멀티 프로세스로 늘릴 수 있는 방법을 제공한다.

![출처: pm2 홈페이지 / cluster mode](https://armadillo-dev.github.io/assets/images/pm2-cluster-mode.png)



애플리케이션을 실행하면 처음에는 마스터 프로세스만 생성하는데 이때 CPU 개수만큼 워커 프로세스를 생성하고 마스터 프로세스와 워커 프로세스가 각각 수행해야 할 일들을 정리해서 구현하면 된다.

> 예를 들어 워커 프로세스가 생성됐을 때 온라인 이벤트가 마스터 프로세스로 전달되면 어떻게 처리할지, 워커 프로세스가 메모리 제한선에 도달하거나 예상치 못한 오류로 종료되면서 종료(exit) 이벤트를 전달할 땐 어떻게 처리할지, 그리고 애플리케이션의 변경을 반영하기 위해 재시작해야 할 때 어떤 식으로 재시작을 처리할 지 등등 고민할 게 많다.



이러한 고민을 해결해 주는 것이 PM2라는 Node.js의 프로세스 매니저이다.



![img](https://engineering.linecorp.com/wp-content/uploads/2019/05/image-3.png)

pm2를 아무런 옵션없이 실행하면 pm2는 기본 모드인 포크모드로 애플리케이션을 실행한다.

모든 CPU를 사용하기 위해서는 애플리케이션을 클러스터 모드로 실행해야 한다.

이는 다음과 같은 설정 파일을 통해 구현할 수 있다.

```js
//ecosystem.config.js
module.exports = {
  apps: [{
  name: 'app',
  script: './app.js',
  instances: 0,
  exec_mode: ‘cluster’
  }]
}
```

이 파일의 기본적인 골격의 경우 `pm2 init` 을 통해 생성할 수 있다.

완성된 설정파일을 활용해 애플리케이션을 클러스터 모드로 실행할 수 있다.

`exec_mode` 항목값을 'cluster'로 설정하면 애플리케이션을 클러스터 모드로 실행하겠다는 의미이고, `instance` 항목값을 '0'으로 설정하면 CPU 코어 수 만큼 프로세스를 생성하겠다는 뜻이다.

위와 같은 설정을 통해 pm2를 실행하면 다음과 같은 결과를 볼 수 있다.

![img](https://engineering.linecorp.com/wp-content/uploads/2019/05/image-4.png)



만약 프로세스 개수를 늘리거나(scale up) 줄여야(scale down) 한다면 `pm2 scale` 명령어를 사용해서 실시간으로 프로세스 수를 증가시키거나 감소시킬 수 있다. 

```js
//코드6. 프로세스 늘리기
$ pm2 scale app +4
[PM2] Scaling up application
[PM2] Scaling up application
[PM2] Scaling up application
[PM2] Scaling up application
```



만약 파일의 내용을 수정한 뒤 수정한 사항을 프로세스에 반영하고 싶다면 프로세스를 재시작해야 한다.

이는 `pm2 reload` 명령어를 사용하여 프로세스를 재시작할 수 있다.



`pm2 list` 의 경우 프로세스 상태를 간략하게 표시해 준다.



### 서비스 운영하기

---

서비스는 오픈 이후에도 여러 상황 변화에 따라 지속적으로 변경해야 한다.

애플리케이션에 새로운 기능을 추가했거나 발견된 버그를 수정했다면, 이를 실제 서비스에 반영하기 위해선 다시 배포해야 한다.

또한 배포가 완료된 후에는 애플리케이션의 변경을 반영하기 위해서 기존  프로세스를 재시작해야 한다.

이때 `reload` 명령어를 활용하면 프로세스를 재시작할 수 있는데, 무중단 서비스를 유지하려면 몇 가지 주의사항이 있다.



### 프로세스 재시작 과정

---

![img](https://engineering.linecorp.com/wp-content/uploads/2019/05/image2019-5-9_16-29-13.png)

프로세스가 10개가 실행되고 있다고 가장해 보면 `pm2 reload` 를 실행하면 pm2는 기존 0번 프로세스를   프로세스로 옯겨두고 새로운 0번 프로세스를 만든다.

새로운 0번 프로세스는 요청을 처리할 준비가 되면 마스터 프로세스에게 `ready` 이벤트를 보내고, 마스터 프로세스는 더 이상 필요없어진 `_old_0` 프로세스에게 `SIGINT` 시그널을 보내고 프로세스가 종료되기를 기다린다.

만약  `SIGINT` 시그널을 보내고 난 후 일정 시간이 지났는데도 종료되지 않는다면,  `SIGKILL` 시그널을 보내 프로세스를 강제로 종료한다.

0번 프로세스의 재시작은 위와 같은 과정을 거쳐 완료되는데, 이 과정을 총 프로세스 개수만큼 반복하면 모든 프로세스의 재시작이 완료된다.



###  재시작 과정에서 서비스 중단이 발생하는 경우

---



#### 새로 만들어진 프로세스가 실제로는 아직 요청을 받을 준비가 되지 않았는데 ready 이벤트를 보내는 경우

----

![img](https://engineering.linecorp.com/wp-content/uploads/2019/05/image2019-5-9_16-32-47.png)

위의 사진과 같은 경우 앱 구동이 완료되지 않았는데도 `_old_0`  프로세스가 종료된다.

이 문제를  해결하기 위해서는 프로세스가 수행될 때 바로 ready 이벤트를 보내지 말고, 애플리케이션 로직에서 요청 받을 준비가 완료된 시점에 ready 이벤트를 보내도록 처리해야 한다.

그리고 마스터 프로세스가 ready 이벤트를 언제까지 기다리게 할 것인지도 설정 파일에 명시해야 한다.

아래 '코드10'은 '코드2'의 `app.js`와 '코드4'의 `ecosystem.config.js`에 관련 코드를 추가한 코드이다.

`ecosystem.config.js`에서 `wait_ready` 옵션을 'true'로 설정하면 마스터 프로세스에게 ready 이벤트를 기다리라는 의미이다.



```js
//ecosystem.config.js
module.exports = {
  apps: [{
  name: 'app',
  script: './app.js',
  instances: 0,
  exec_mode: ‘cluster’,
  wait_ready: true,
  listen_timeout: 50000
  }]
}
```

```js
//app.js
const express = require('express')
const app = express()
const port = 3000
app.get('/', function (req, res) { 
  res.send('Hello World!')
})
app.listen(port, function () {
  process.send(‘ready’)
  console.log(`application is listening on port ${port}...`)
})
```



아래는 위의 설정을 변경한 후의 구동 모습이다.

![img](https://engineering.linecorp.com/wp-content/uploads/2019/05/image2019-5-9_16-37-7.png)



#### 클라이언트 요청을 처리하는 도중에 프로세스가 죽어버리는 경우

![img](https://engineering.linecorp.com/wp-content/uploads/2019/05/image2019-5-9_16-38-12.png)

`reload` 명령어를 실행할 때, 기존 0번 프로세스인 `_old_0` 프로세스는 프로세스가 종료되기 전까진 계속해서 사용자 요청을 받는다.

그런데 만약 `SIGINT` 시그널이 전달된 상태에서 사용자 요청을 받았고, 그 요청을 처리하는 데 5000ms가 걸린다고 가정해보자.

앞서 말씀드렸듯, `SIGINT` 시그널을 받은 뒤 1600ms 이후에도 종료하지 않는다면 `SIGKILL` 시그널을 받고 강제 종료된다.

따라서 5000ms가 걸리는 사용자 요청을 처리하는 도중 SIGKILL 시그널을 받고 사용자에게 응답을 보내주지 못한 채 종료될 테고, 프로세스가 강제 종료되었기 때문에 클라이언트와의 연결은 끊어지게 된다.

이런 경우에도 서비스가 중단된다.



이 문제를 해결하기 위해서는 `SIGINT` 시그널을 리스닝(listening)하다가 해당 시그널이 전달되면 `app.close`명령어로 프로세스가 새로운 요청을 받는 것을 거절하고 기존 연결은 유지하게 처리합니다.

그리고 사용자 요청을 처리하기에 충분한 시간을 `kill_timeout`에 설정하고, 기존에 유지되고 있던 연결이 종료되면 프로세스가 종료되도록 처리한다.

아래 '코드11'처럼 `ecosystem.config.js`에서 `kill_timeout` 옵션을 '5000'으로 설정하면 SIGINT 시그널을 보낸 후 프로세스가 종료되지 않았을 때 SIGKILL 시그널을 보내기까지의 대기 시간을 디폴트 값 1600ms에서 5000ms로 변경할 수 있다.

`app.js`에 새로 추가된 코드는 해당 프로세스에 SIGINT 시그널이 전달되면, 새로운 요청을 더 이상 받지 않고 연결되어 있는 요청이 완료된 후 해당 프로세스를 강제로 종료하도록 처리하는 코드이다.

```js
//코드11. 클라이언트 요청 처리 설정
//ecosystem.config.js
module.exports = {
  apps: [{
  name: 'app',
  script: './app.js',
  instances: 0,
  exec_mode: ‘cluster’,
  wait_ready: true,
  listen_timeout: 50000,
  kill_timeout: 5000
  }]
}
```

```js
//app.js
const express = require('express')
const app = express()
const port = 3000
app.get('/', function (req, res) { 
  res.send('Hello World!')
})
app.listen(port, function () {
  process.send(‘ready’)
  console.log(`application is listening on port ${port}...`)
})
process.on(‘SIGINT’, function () {
  app.close(function () {
  console.log(‘server closed’)
  process.exit(0)
  })
})
```



`app.close`를 이용해 새로운 요청을 거절하고 이미 연결되어 있는 건 유지할 때, 만약 아래 그림과 같이 '[HTTP 1.1 Keep-Alive](https://en.wikipedia.org/wiki/Keepalive)'를 사용하고 있었다면, 요청이 처리된 후에도 기존 연결이 계속 유지되기 때문에 앞의 방법만으로는 해결되지 않는다.



![img](https://engineering.linecorp.com/wp-content/uploads/2019/05/image2019-5-9_16-39-46.png)



이때는 아래 '코드12'와 같이 SIGINT 시그널을 받았을 때 특정 전역 플래그값에 따라 응답 헤더에 'Connection: close'를 설정해 클라이언트 요청을 종료하는 방법을 활용, 타임아웃으로 서비스가 중단되는 문제를 해결할 수 있다.

```js
//코드12. 특정 전역 플래그값에 따른 응답 헤더 설정
//app.js
const express = require('express')
const app = express()
const port = 3000
let isDisableKeepAlive = false
app.use(function(req, res, next) {
  if (isDisableKeepAlive) {
    res.set(‘Connection’, ‘close’)
  }
  next()
})
app.get('/', function(req, res) { 
  res.send('Hello World!')
})
app.listen(port, function() {
  process.send(‘ready’)
  console.log(`application is listening on port ${port}...`)
})
process.on(‘SIGINT’, function () {
  isDisableKeepAlive = true
  app.close(function () {
  console.log(‘server closed’)
  process.exit(0)
  })
})
```



<details>
<summary>참고</summary>
<a href=https://engineering.linecorp.com/ko/blog/pm2-nodejs/>https://engineering.linecorp.com/ko/blog/pm2-nodejs/</a></br>
</details>




