# PWA

- pwa
  - pwa란?
  - pwa 구조
    - Swervice Worker
  - Manifest
  - Notification, Push



## pwa란?

---

PWA(progressive web app)는 웹과 네이티브 앱의 기능 모두의 이점을 갖도록 수 많은 특정 기술과 표준 패턴을 사용해 개발된 웹 앱이다.

이는 완전히 새로운 개념은 아니다.

pwa와 같은 아이디어는 과거에 다양한 접근 방법으로 웹 플랫폼상에서 검토되었다.

점진적인 향상과 반응형 디자인은 이미 우리가 모바일 친화적인 웹사이트를 구축할 수 있게 해주고 있다.

오프라인에서 동작하는 것과 앱을 설치하는 것은 Firefox OS 생태계에서 이미 가능하였다.

하지만, pwa는 웹을 만드는 이미 존재하는 어떤 기능도 제거하지 않은 상태로 이 모든 것 그리고 이상을 제공한다.



pwa는 하나의 기술로 생성된 것이 아니다.

그렇기에 pwa는 다음과 같은 **기능적** 요구 사항이 존재한다.

- 오프라인에서 동작
- 설치 가능
- 쉬운 동기화 
- 푸시 알림

앱의 완성도를 퍼센트로 측정하는 도구도 존재한다.

크롬의 개발자 도구에서 확인 할 수 있는 Lighthouse가 대표적이다.



웹 앱이 pwa로 식별되기 위한 **핵심 원칙**은 다음과 같다.

- 발견가능 : 컨텐츠를 검색 엔진을 통해 찾을 수 있다.
- 설치가능 : 기기의 홈 화면에서 사용할 수 있다.
- 연결가능 : URL 전송을 통해 공유할 수 있다.
- 네트워크 독립적 : 오프라인이나 불안한 네트워크 연결에서 동작한다.
- 점진적 : 최신 브라우저의 모든 기능을 사용할 수는 없지만 이전 브라우저의 기능은 사용 가능하다.
- 재참여가능(Re-engageable) : 새 컨텐츠가 사용 가능할 때마다 알림을 보낼 수 있다.
- 반응형 : 모든 기기의 화면이나 브라우저에서 사용할 수 있다.
- 안전 : 민감한 데이터에 접근하려는 모든 제 3자로부터 안전하다.



브라우저 호환성을 위해 pwa는 service worker를 사용하고 있다.

service worker는 현제 데스크탑 및 모바일의 모든 주요 브라우저에서 지원된다.



또, 웹 앱 Manifest, 푸시, 알림 그리고 홈 화면에 추가 기능과 같은 다른 기능들도 지원한다.

다만 현재 Safari에서는 웹 앱 Manifest 및 홈 화면에 추가에 지원이 제한적이며 웹 푸시 알림은 지원하지 않는다.

하지만 다른 주요 브라우저에서는 모든 기능들을 지원한다.



## pwa 구조

---

웹 사이트를 렌더링하는 방법은 서버 사이드와 클라이언트 사이드라는 2가지 다른 접근 방법이 있다.



### 서버사이드 렌더링(SSR)

---

웹사이트가 서버에서 렌더링되는 것을 의미한다.

따라서 빠른 첫 로딩을 제공할 수 있지만, 페이지 간의 이동에서 모든 것을 매번 다운로드 해야한다.

각 페이지를 로딩할 때마다 서버를 거쳐야 한다는 점에서 로딩 속도 및 성능으로 인식되는 일반적인 측면에서 어려움이 있다.



### 클라이언트 사이드 렌더링(CSR)

---

웹 사이트가 다른 페이지로 이동할 때 브라우저에서 거의 즉시 업데이트될 수 있도록 해주지만, 시작할 때 더 많은 초기 다운로드와 추가 렌더링이 필요하다.

웹 사이트 첫 방문시 더 느리지만 다음 방문에서는 훨씬 더 빠른 것이 장점이다.



SSR과 CSR을 혼합하여 서버에서 웹 사이트를 렌더링하고, 컨텐츠를 캐싱한 후, 필요할 때 클라이언트 사이드에서 렌더링을 업데이트하여 최고의 결과를 이끌어 낼 수 있다. 

첫 페이지 로딩은 SSR 때문에 빠르고, 페이지 간의 이동은 클라이언트에서 변경된 부분만 다시 렌더링하므로 부드럽다.

이러한 SSR과 CSR을 혼합한 방법을 "app shell"이라 한다.



### App shell

---

App shell 개념은 가능한 최소한의 사용자 인터페이스를 로딩하는 것에 중점을 둔다.

캐싱을 통해 다음 방문에서 앱의 모든 컨텐츠가 로딩되기 전에 오프라인에서도 사용가능한다. 

이렇게 하면 다음에 앱을 실행할 때, UI는 캐시로 즉시 로딩되고 새로운 컨텐츠는 서버로부터 요청한다.



이 구조는 빠르고, 사용자가 로딩 스피너나 빈 페이지 대신 "무언가"를 즉시 보게됨으로써 속도가 빠름을 느낄 수 있다.

또한 네트워크 연결이 불가할 때 웹 사이트를 오프라인에서도 접근할 수 있게 된다.



### 예제 어플리케이션의 구조

---

![Folder structure of js13kPWA.](https://mdn.mozillademos.org/files/15925/js13kpwa-directory.png)



위의 구조는 글을 작성하기 위해 참고하고 있는 mdn에서 제공한 예제 어플리케이션의 구조이다.



이 글에서는 service worker를 위주로 살펴 보려한다.



#### Service Worker

---

우선 Service Worker은 브라우저와 네트워크 사이의 가상의 프록시라 할 수 있다.

이는 페이지의 메인 JavaScript 코드와 독립된 스레드에서 실행되며, DOM 구조에 대한 어떤 접근도 갖지 않는다.

이는 전통적인 웹 프로그래밍과 다른 접근법을 가진다.

우리는 Service Worker에 어떤 작업을 전달할 수 있으며, Promise 기반 접근법을 사용해 결과가 준비될 때마다 전달받을 수 있다.



Service Worker는 단지 오프라인 기능을 제공하는 것 이상으로 알림 처리, 독립된 스레드에서의 복잡한 계산 등 많은 것을 할 수 있다.

Service Worker는 네트워크 요청을 제어하고 수정하며, 캐시로부터 반환된 커스텀 응답을 제공하거나 응답을 완전히 가공할 수도 있으므로 매우 강력하다.

그렇기에 Service Worker는 HTTPS에서만 실행된다. 



##### Service Worker 등록

---

```javascript
if('serviceWorker' in navigator) {
    navigator.serviceWorker.register('/pwa-examples/js13kpwa/sw.js');
};
```

우선 service worker를 사용하기 위해 위와 같은 코드를 app.js에 등록한다.

컨텐츠는 sw.js 파일안에 있으며 등록이 성공한 후에 실행된다.

app.js 파일안에 있는 Service Worker 코드의 유일한 부분이다.

Service Worker에 대한 다른 모든 것들은 sw.js 파일에 작성된다.



이제 위에서 등록한 파일인 sw.js에 대해 알아보자.



##### Service Worker 설치

----

```js
self.addEventListener('install', function(e) {
    console.log('[Service Worker] Install');
});
```

`install` 리스러에 캐시를 초기화하고 오프라인에서 사용을 위한 파일들을 추가할 수 있다.



먼저, 캐시 이름을 저장할 변수와 app shell 파일들을 배열에 나열한다.

```js
var cacheName = 'js13kPWA-v1';
var appShellFiles = [
  '/pwa-examples/js13kpwa/',
  '/pwa-examples/js13kpwa/index.html',
  '/pwa-examples/js13kpwa/app.js',
  '/pwa-examples/js13kpwa/style.css',
  '/pwa-examples/js13kpwa/fonts/graduate.eot',
  '/pwa-examples/js13kpwa/fonts/graduate.ttf',
  '/pwa-examples/js13kpwa/fonts/graduate.woff',
  '/pwa-examples/js13kpwa/favicon.ico',
  '/pwa-examples/js13kpwa/img/js13kgames.png',
  '/pwa-examples/js13kpwa/img/bg.png',
  '/pwa-examples/js13kpwa/icons/icon-32.png',
  '/pwa-examples/js13kpwa/icons/icon-64.png',
  '/pwa-examples/js13kpwa/icons/icon-96.png',
  '/pwa-examples/js13kpwa/icons/icon-128.png',
  '/pwa-examples/js13kpwa/icons/icon-168.png',
  '/pwa-examples/js13kpwa/icons/icon-192.png',
  '/pwa-examples/js13kpwa/icons/icon-256.png',
  '/pwa-examples/js13kpwa/icons/icon-512.png'
];
var gamesImages = [];
for(var i=0; i<games.length; i++) {
  gamesImages.push('data/img/'+games[i].slug+'.jpg');
}
var contentToCache = appShellFiles.concat(gamesImages);
```



> pwa builder를 사용하여 생성한 pwabuilder-sw.js 에서의 코드
>
> ```js
> workbox.routing.registerRoute(
>   new RegExp('/*'),
>   new workbox.strategies.StaleWhileRevalidate({
>     cacheName: CACHE
>   })
> );
> ```
>
> workbox를 통해 정규식을 활용하여 모든 파일을 캐싱하고 cachename을 설정하고 있다.



이렇게 데이터를 생성하면 `install` 이벤트 자체를 관리할 수 있게 된다.

```js
self.addEventListener('install', function(e) {
  console.log('[Service Worker] Install');
  e.waitUntil(
    caches.open(cacheName).then(function(cache) {
          console.log('[Service Worker] Caching all: app shell and content');
      return cache.addAll(contentToCache);
    })
  );
});
```

위의 코드에서 주목해야할 것은 `e.waitUntil` 그리고 `cache` 객체이다.



Service Worker는 `waitUntil` 안의 코드가 실행되기전까지 설치되지 않는다.



`cache` 는 데이터를 저장할 수 있도록 주어진 Service Worker의 범위 내에서 사용할 수 있는 특별한 객체이다.

웹 저장소는 동기적이므로 웹 저장소로의 저장은 동작하지 않는다.

Service Worker는 Cache API를 대신 적용한다.



주어진 이름(`cacheName`)으로 캐시를 열고 캐시에서 사용할 앱의 모든 파일을 추가하여(`cache.addAll(contentToCache)`) 다음 로딩에서 사용할 수 있도록 한다.



##### Service Worker 패치 응답

---

처리를 위한 `fetch` 이벤트도 있다.

이는 앱으로부터 HTTP 요청이 생성될 때 마다 발생한다.

이는 요청을 가로채 커스텀 응답할 수 있어 매우 유용하다.

다음은 간단한 예시이다.

```js
self.addEventListener('fetch', function(e) {
    console.log('[Service Worker] Fetched resource '+e.request.url);
});
```



이를 활용하면 가능한 경우 다음과 같이 캐시로부터 컨텐츠를 패치하여 오프라인 기능을 제공할 수 있는데 이를 위한 코드는 다음과 같다.

```js
self.addEventListener('fetch', function(e) {
  e.respondWith(
    caches.match(e.request).then(function(r) {
      console.log('[Service Worker] Fetching resource: '+e.request.url);
      return r || fetch(e.request).then(function(response) {
        return caches.open(cacheName).then(function(cache) {
          console.log('[Service Worker] Caching new resource: '+e.request.url);
          cache.put(e.request, response.clone());
          return response;
        });
      });
    })
  );
});
```

위의 코드는 리소스를 찾고 존재할 경우 응답을 반환하는 함수로 패치 이벤트에 응답한 것이다. 

응답잉 없을 경우 다른 패치 요청을 사용해 네트워크로부터 패치한 후 캐시에 응답을 저장하여 다음 요청에서 사용할 수 있도록 한다.



`e.respondWith` 메소드가 제어를 대신한다.

이는 앱과 네트워크 사이의 프록시 서버로서 기능하는 부분이다.

이는 Service Worker에 의해 준비되고, 캐시로부터 가져와 필요한 경우 수정하여 모든 요청에 대해 우리가 원하는 응답을 할 수 있게 해준다.



> pwa builder를 사용하여 생성한 pwabuilder-sw.js 에서의 코드
>
> ```js
> self.addEventListener('fetch', (event) => {
>   if (event.request.mode === 'navigate') {
>     event.respondWith((async () => {
>       try {
>         const preloadResp = await event.preloadResponse;
> 
>         if (preloadResp) {
>           return preloadResp;
>         }
> 
>         const networkResp = await fetch(event.request);
>         return networkResp;
>       } catch (error) {
> 
>         const cache = await caches.open(CACHE);
>         const cachedResp = await cache.match(offlineFallbackPage);
>         return cachedResp;
>       }
>     })());
>   }
> });
> ```



##### Service Worker 업데이트

---

Service Worker의 업데이트는 캐시 이름안의 버전 넘버가 핵심이다.

```js
var cacheName = 'js13kPWA-v1';
```

위와 같이 버전을 업데이트했을 때, 새 캐시에 모든 파일을 추가할 수 있다.



그렇다면 삭제는 어떻게 할까?

`activate` 이벤트를 활용한다.

```js
self.addEventListener('activate', function(e) {
  e.waitUntil(
    caches.keys().then(function(keyList) {
          return Promise.all(keyList.map(function(key) {
        if(cacheName.indexOf(key) === -1) {
          return caches.delete(key);
        }
      }));
    })
  );
});
```





## Manifest

---

manifest는 앱을 URL을 수동으로 브라우저에 입력하는 대신 기기의 홈 화면에서 바로 실행할 수 있도록 해준다.



manifest는 다음과 같은 요구사항이 있다.

- 웹 manifest
- 보안(HTTPS) 도메인에서 제공되는 웹 사이트
- 기기에서 앱을 나타낼 아이콘
- 앱을 오프라인에서 동작하게 하기 위한 service worker 등록



### manifest 파일

---

JSON 형식으로 웹 사이트에 대한 모든 정보를 나열한 웹 manifest 파일이다.

파일은 일반적으로 웹 앱의 루트 폴더에 위치한다. 

> 본인이 pwa를 배포할 때는 public 폴더에 위치시켰음

앱의 title, 모바일 OS에서 앱을 나타내는데 사용되는 다른 크기의 아이콘들의 경로, 로딩 또는 스플래시 화면에서 사용할 배경 색상과 같은 유용한 정보들을 포함한다.

이는 브라우저가 웹 앱을 설치할 때 그리고 홈 화면에서 웹 앱을 적절히 표현하기 위해 필요한 정보입니다.

이러한 manifes 파일은 다음의 코드를 index.html의 \<head> 섹션에 포함 시켜야 한다.

```js
<link rel="manifest" href="js13kpwa.webmanifest">
```

파일의 내용은 다음과 같이 구성하면 된다.

```js
{
    "name": "js13kGames Progressive Web App",
    "short_name": "js13kPWA",
    "description": "Progressive Web App that lists games submitted to the A-Frame category in the js13kGames 2017 competition.",
    "icons": [
        {
            "src": "icons/icon-32.png",
            "sizes": "32x32",
            "type": "image/png"
        },
        // ...
        {
            "src": "icons/icon-512.png",
            "sizes": "512x512",
            "type": "image/png"
        }
    ],
    "start_url": "/pwa-examples/js13kpwa/index.html",
    "display": "fullscreen",
    "theme_color": "#B12A34",
    "background_color": "#B12A34"
}
```



웹 manifest를 위한 최소 요구 사항은 `name`과 적어도 하나(`src`, `size`, `type`을 포함)의 아이콘이다.

`description`, `short_name`, `start_url`은 권장사항이다.



## Notification, Push

---

앱을 오프라인에서 동작하도록 컨텐츠를 캐싱하는 것은 훌륭한 기능이다.

사용자가 홈 화면에서 웹 앱을 설치하도록 하는 것이 가장 좋지만, 사용자의 동작에 의존하는 댓니 푸시 메시지와 알림을 사용해 새로운 컨텐츠가 있을때 이를 전달하여 사용자를 자동으로 다시 참여하도록 할 수 있다.



Push API와 Notifications API는 두개의 독립된 API이지만, 앱에 재참여 기능을 제공하려 할 때 함께 잘 작동한다.

Push는 클라이언트 사이드의 관여 없이 서버로부터 앱으로 새로운 컨텐츠를 전달하는데 사용되며, 이 작업은 앱의 service worker에 의해 처리된다.

Notification은 service worker에 의해 사용자에게 새로운 정보를 보여주거나 무언가 업데이트 되었음을 알리는데 사용된다.



두 API는 service worker와 마찬가지로 브라우저 창의 바깥에서 동작하기 때문에 앱의 페이지를 보고 있지 않거나  심지어 종료되었을 때도 업데이트 푸시를 보내거나 알림을 보여줄 수 있다.



### Notifications

---



#### 권한 요청

---

알림을 보여주려면 먼저 권한을 요청해야 한다.

즉시 알림을 보여주는 대신 사용자가 버튼을 클릭하여 팝업을 요청할 때 요청 팝업을 보여주는 것이 좋은 사용법이다.

```js
var button = document.getElementById("notifications");
button.addEventListener('click', function(e) {
    Notification.requestPermission().then(function(result) {
        if(result === 'granted') {
            randomNotification();
        }
    });
});
```

사용자가 알림 수신을 확인하면 앱이 알림을 보여줄 수 있다.

승인이 되면, 권한은 알림과 푸시 모두에 대해 동작한다.



#### 알림 생성

---

예시 코드는 다음과 같다.

```js
function randomNotification() {
    var randomItem = Math.floor(Math.random()*games.length);
    var notifTitle = games[randomItem].name;
    var notifBody = 'Created by '+games[randomItem].author+'.';
    var notifImg = 'data/img/'+games[randomItem].slug+'.jpg';
    var options = {
        body: notifBody,
        icon: notifImg
    }
    var notif = new Notification(notifTitle, options);
    setTimeout(randomNotification, 30000);
}
```



### Push

---

푸시는 알림보다 더 복잡하다.

앱으로 데이터를 다시 전송해줄 서버를 구독해야 한다.

앱의 Service Worker는 푸시 서버로부터 데이터를 수신하며 알림 서스템 또는 원하는 경우 다른 메커니즘을 사용해 이를 보여줄 수 있다.



푸시 알림을 수신하려면 Service Worker가 있어야 한다.

이를 위한 코드는 다음과 같다.

```js
registration.pushManager.getSubscription() .then( /* ... */ );
```

사용자가 구독하기 시작하면, 서버로부터 푸시 알림을 받을 수 있다.



서버사이드에서는 보안적인 이유로 전체 프로세스를 공개키와 비공개키를 사용하여 암호화해야 한다.



푸시 메시지를 수신하려면, Service worker 파일에서 `push`이벤트를 사용하면된다.

```js
self.addEventListener('push', function(e) { /* ... */ });
```

데이터는 반환되어 알림으로 사용자에게 즉시 보여진다.



####  index.js

----

`push` 를 위한 Service worker는 다음과 같이 등록한다.

```js
navigator.serviceWorker.register('service-worker.js')
.then(function(registration) {
  return registration.pushManager.getSubscription()
  .then(async function(subscription) {
      // registration part
  });
})
.then(function(subscription) {
    // subscription part
});
```

 이 특정 케이스에서는, 등록한 후에 등록 객체를 사용해 구독하며, 그 후 구독 객체를 사용해 전체 프로세스를 완료한다.



등록 파트의 코드는 다음과 같다.

```js
if(subscription) {
    return subscription;
}
```

사용자가 이미 구독했을경우, 구독 객체를 반환하며 구독 파트로 이동한다.

그렇지 않을 경우 새로운 구독을 초기화 한다.



```js
const response = await fetch('./vapidPublicKey');
const vapidPublicKey = await response.text();
const convertedVapidKey = rlBase64ToUint8Array(vapidPublicKey);
```

앱은 서버의 공개 키를 패치하고 응답을 텍스트로 변환한다.



```js
return registration.pushManager.subscribe({
    userVisibleOnly: true,
    applicationServerKey: convertedVapidKey
});
```

앱은 이제 새로운 사용자를 구독하기 위해 `PushManager`를 사용한다.

두 옵션이 `PushManager.subscribe()`)메소드로 전달된다.

첫 번째는 `userVisibleOnly: true`이며 사용자에게 전송되는 모든 알림이 보여진다는 것을 의미하며, 두 번째 것은 `applicationServerKey`이며 성공적으로 획득하고 변환된 VAPID 키를 포함한다.



```js
fetch('./register', {
    method: 'post',
    headers: {
        'Content-type': 'application/json'
    },
    body: JSON.stringify({
        subscription: subscription
    }),
});
```

구독 파트의 경우 앱은 Fetch를 사용해 먼저 서버로 JSON으로 된 구독 상세 정보를 전송한다.



그 다음 구독 버튼의 `onclick` 이 정의된다.

```js
document.getElementById('doIt').onclick = function() {
    const payload = document.getElementById('notification-payload').value;
    const delay = document.getElementById('notification-delay').value;
    const ttl = document.getElementById('notification-ttl').value;

    fetch('./sendNotification', {
        method: 'post',
        headers: {
            'Content-type': 'application/json'
        },
        body: JSON.stringify({
            subscription: subscription,
            payload: payload,
            delay: delay,
            ttl: ttl,
        }),
    });
};
```

버튼이 클릭되면, `fetch`는 제공된 파라미터를 사용해 서버로 알림을 전송합니다. `payload`는 알림에 표시될 텍스트이고, `delay`는 알림이 보여질 때 까지의 지연 시간을 정의하며, `ttl`은 지정된 시간(초 단위로도 정의됨)동안 서버에서 알림을 사용할 수 있게하는 time-to-live 설정이다.



#### server.js

---

web-push 모듈은 VAPID 키를 설정하기 위해 사용되며, 존재하지 않을 경우 선택적으로 생성한다.

```js
const webPush = require('web-push');

if (!process.env.VAPID_PUBLIC_KEY || !process.env.VAPID_PRIVATE_KEY) {
  console.log("You must set the VAPID_PUBLIC_KEY and VAPID_PRIVATE_KEY "+
    "environment variables. You can use the following ones:");
  console.log(webPush.generateVAPIDKeys());
  return;
}

webPush.setVapidDetails(
  'https://serviceworke.rs/',
  process.env.VAPID_PUBLIC_KEY,
  process.env.VAPID_PRIVATE_KEY
);
```



그 다음, 모듈은 앱이 처리해야할 모든 라우트(VAPID 공개 키 얻기, 등록 후 알림 보내기)를 정의하고 내보낸다.

```js
module.exports = function(app, route) {
  app.get(route + 'vapidPublicKey', function(req, res) {
    res.send(process.env.VAPID_PUBLIC_KEY);
  });

  app.post(route + 'register', function(req, res) {

    res.sendStatus(201);
  });

  app.post(route + 'sendNotification', function(req, res) {
    const subscription = req.body.subscription;
    const payload = req.body.payload;
    const options = {
      TTL: req.body.ttl
    };

    setTimeout(function() {
      webPush.sendNotification(subscription, payload, options)
      .then(function() {
        res.sendStatus(201);
      })
      .catch(function(error) {
        console.log(error);
        res.sendStatus(500);
      });
    }, req.body.delay * 1000);
  });
};
```



#### service-worker.js

---

```js
self.addEventListener('push', function(event) {
    const payload = event.data ? event.data.text() : 'no payload';
    event.waitUntil(
        self.registration.showNotification('ServiceWorker Cookbook', {
            body: payload,
        })
    );
});
```

위의 코드는 `push`  이벤트에 리스너를 추가하고, 데이터로부터 받은 텍스트로 구성된 paylod 변수를 생성한 후, 사용자에게 보여줄 알림을 보여주는 코드이다.



<details>
<summary>참고</summary>
<a href=https://developer.mozilla.org/ko/docs/Web/Progressive_web_apps </a>핵심 PWA 가이드
</details>
