## 비동기 처리

---



우선 동기와 비동기의 차이를 먼저 알아보자.

동기식은 먼저 시작된 하나의 작업이 끝날 때까지 다른 작업을 시작하지 않고 기다렸다고 다 끝나면 새로운 작업을 시작하는 방식이다.

비동기 방식은 먼저 시작된 작업의 완료 여부와는 상관없이 새로운 작업을 시작하는 방식이다.



### 콜백 함수

---

우선 콜백 함수란 이름 그대로 나중에 호출될 함수를 말한다.

**즉, 콜백 함수는 코드를 통해 명시적으로 호출하는 함수가 아니라, 개발자는 단지 함수를 등록하기만 하고, 어떤 이벤트가 발생했거나 특정 시점에 도달했을 때 시스템에서 호출하는 함수를 말한다.**



우리가 콜백 함수를 사용하는 이유는, 아래의 예제 코드처럼 자바스크립트에서 콜백함수를 통해 비동기적 프로그래밍을 할 수 있기 때문이다.

```js
function printString(callbackParam) {
  console.log(callbackParam);
}

function callPrint(callback) {
  let value;
  
  console.log("Wait 3 sec.");
  console.log("waiting...");
  
  setTimeout(function() {
    value = "Hello";
    callback(value);
  }, 3000);
}

// 실행
callPrint(printString);
```



이 코드는 다음과 같은 결과를 얻을 수 있다.

```
Wait 3 sec.
waiting...
// 3초후
Hello
```



사실 본인은 위의 코드의 결과는 이해하지만 구체적인 구동 과정은 이해가 가지 않아 다음과 같이 이해하기로 하였다.

우선 콜백정의함수와 콜백수행함수로 나누어 이해하기로 했다.



위의 코드에서 **콜백정의함수**는 `printString` 이다.

콜백정의함수는 콜백수행함수에서 처리한 결과값을 처리하는 함수를 정의한다.

위의 경우 `value` 를 전달받아 콜백정의함수에서 정의한 `console.log(callbackParam);` 를 수행한다.



위의 코드에서 **콜백수행함수**는 `callPrint` 이다.

콜백수행함수는 위에서 설명한 것과 같이 콜백정의함수에서 처리할 결과값을 만드는 역활을 한다.

위의 경우 별다른 처리는 하지 않고 `value = "Hello"` 와 같이 값을 대입하고 콜백정의함수에게 전달해 준다.

이때 콜백정의함수는 위와 같이 `callback(value)` 일 수도 있고 `done(value)` 일 수도 있다. 

사실상 2개 모두 같은 것이고 괄호 속의 `value` 가 콜백정의함수에 전달되어 수행된다는 것만 알고 있으면 된다.



위의 예시만 보면 콜백함수는 나중에 실행되는 함수라는 생각을 할 수 있다.

하지만 콜백함수가 꼭 나중에 실행되는 함수는 아니다.

위의 예제를 다음과 같이 고쳐보자.

```js
function printString(callbackParam) {
    console.log(callbackParam);
  }
  
  function callPrint(callback) {
    let value;

    console.log("Wait 3 sec.");
    console.log("waiting...");
    
    value = "Hello1";
    callback(value);

  }
  
  // 실행
  callPrint(printString);
```



결과는 위의 코드와 같다.

하지만 "Hello"가 3초 후에 나오는 것이 아닌 바로 나오게 된다.



### Promise

---

Promise는 생성된 시점에는 알려지지 않았을 수도 있는 값을 위한 것으로, 비동기 연산이 종료된 이후에 결과 값과 실패 사유를 처리하기 위한 처리기를 연결할 수 있다.

프로미스를 사용하면 비동기 메서드에서 마치 동기 메서드처럼 값을 반환할 수 있다.

다만 최종 결과를 반환하는 것이 아니고, 미래의 어떤 시점에서 결과를 제공하겠다는 '**약속**'을 반환한다.



그렇기에 `Promise`는 다음 중 하나의 상태를 가진다.

- Pending
- Fullfiled
- Rejected



대기 중인 프로미스는 값과 함께 이행할 수도, 어떤 이유로 인해 거부될 수도 있다.

이행이나 거부될 때, 프로미스의 `then` 메서드에 의해 대기열에 추가된 처리기들이 호출된다.

이미 이행했거나 거부된 프로미스에 처리기를 연결해도 호출되므로, 비동기 연산과 처리기 연결 사이에 경합 조건은 없다.



![img](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/promises.png)



이제 Promise 예제 코드를 보자.

```js
function goToSchool() {
    console.log("학교에 갑니다.");
}

function arriveAtSchool_tobe_adv() {
    return new Promise(function(resolve, reject){
        setTimeout(function() {
            var status = Math.floor(Math.random()*2);
            if(status === 1) {
                resolve("학교에 도착했습니다.");
            } else {
                reject("중간에 넘어져 다쳤습니다.");
            }
        }, 1000);
    });
}

function study() {
    console.log("열심히 공부를 합니다.");
}

function cure() {
    console.log("양호실에 가서 약을 발랐습니다.");
}


goToSchool();
arriveAtSchool_tobe_adv()
.then(function(res){
    console.log(res);
    study();
})
.catch(function(err){
    console.log(err);
    cure();
});
```



위의 `arriveAtSchool_tobe_adv()` 는 Promise 객체를 반환한다.

이 Promise 객체는 `setTimeout` 이라는 함수가 수행되며 만들어진다.

만약 `setTimeout` 이 성공하면 `resolve` 를 실패하면 `reject` 를 반환한다.

`resolve` 는 `.then` 을 통해 그 값을 처리할 수 있고 `reject` 는 `.catch` 를 통해 그 값을 처리할 수 있다.



#### Promise.all()

---

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F1yV49%2FbtqGYpxYRYP%2FzuhA7oic2ZxkZVQpQCwKT1%2Fimg.png" alt="img" style="zoom:50%;" />



위의 사진이 `Promise.all` 에 대해 잘 나타내는 사진인 것 같다.

`Promise.all` 메서드는 순회 가능한 객체에 주어진 모든 프로미스가 이행된 후, 혹은 프로미스가 주어지지 않았을  때 이행하는 Promise를 반환한다.

주어진 프로미스 중 하나가 거부하는 경우, 첫 번째로 거절한 프로미스의 이유를 사용해 자신도 거부한다.



그렇기에 `Promise.all` 은 여러 개의 프로미스를 처리할 때 사용하는 것이 좋고 모든 프로미스가 이행될 때까지 기다렸다가 그 결과 값을 담은 배열을 반환하는 메서드라고 볼 수 있다.



다음은 이해를 돕기 위한 예제이다.

```js
let promise = Promise.all([...promises...]);
```

```js
function run(ms){
	return new Promise(resolve =>setTimeout(resolve, ms));
}

const add =async ()=>{
	await run(1000);
  	return 'add success!';
}

const update =async ()=>{
	await run(2000);
  	return 'updated!';
}

const delete =async ()=>{
	await run(3000);
  	return 'del!';
}

async function process(){
	const results = await Promise.all([add(),update(),delete()]);  
  	console.log(results);
}
	
process()
```

```
["add success!", "update!", "del!"]
```



`process()` 를 실행했을 때 `Promise.all` 에 의해 `add()` , `update()` 그리고 `delete()` 가 동시에 실행되고 그 결과가 `results` 에 담긴다.



프로젝트를 진행하면서 리펙토링 중` Promise.all`을 사용한 경우도 살펴보자.

```js
var img = await Promise.all(record.rows.map(
    rows => Image.findOne({where : {RecordId : rows.id}, raw : true})
))
```

`record.rows.map()` 에 의해 rows의 수만큼 `Image.findOne` 요청이 발생한다. 

이는 여러 개의 프로미스를 처리해야하고 모든 프로미스가 이행될 때까지 기다렸다가 그 결과 값을 담아야 하는 위의 코드의 특성상 `Promise.all` 을 사용하는데 좋은 경우라고 할 수 있다.







<details>
<summary>참고</summary>
<a href=https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all </a>https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all </br>
<a href=https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise</a>https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise </br>
<a href=https://sangminem.tistory.com/284</a>https://sangminem.tistory.com/284 </br>
<a href=https://velog.io/@gsuchoi/JavaScript-Promise.all</a>https://velog.io/@gsuchoi/JavaScript-Promise.all </br>
</details>
