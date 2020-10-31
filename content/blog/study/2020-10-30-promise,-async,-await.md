---
title: 2020-10-30 Promise, Async, Await
date: 2020-10-30 02:10:53
category: study
draft: false
---

자바스크립트를 사용한 웹 개발에서 핵심 개념 중 하나라고 할 수 있는 `Promise`를 정리하면서, Promise Chaining의 불편함을 보완하기 위해 ES7에 도입된 `async`/`await` 문법에 대해 정리해보자.

지난 5월에 한번 정리한 적이 있긴 하지만([링크](https://saengmotmi.netlify.app/study/2020-05-15-promise/)) 부족한 설명이 많고, 나 역시 새롭게 알게 된 내용이 있으므로 한번 더 정리하면 좋을 듯 하다. :)

### **읽기 전에!**

자바스크립트 엔진의 비동기 처리에 관한 동작에 대한 간단한 이해가 필요하다. gif 자료를 곁들인 훌륭한 [자료](https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif)가 있으니 참고하자.

# Promise!

모든 기술은 이전에 존재했던 기술의 단점을 딛고서 등장했다고 해도 과언이 아니다. `Promise` 역시 그렇다. 일전에 MDN에 작성된 정의를 참고하여 다음과 같이 옮긴 바 있다.

> Promise 객체는 비동기 작업이 맞이할 미래의 완료 또는 실패와 그 결과 값을 나타냅니다. (출처 : MDN)

위 정의롤 보고 알 수 있듯 `Promise`는 자바스크립트에서 비동기 작업을 처리할 때 쓰이는 객체다.

비동기 작업이란 코드는 순차적으로 실행되지만, 결과를 지금 당장 알 수는 없고 나중에서야 결정되는 동작을 뜻한다. 대표적으로 서버와의 통신이 있다. 서버로 요청을 발신하고, 서버로부터 요청에 대한 응답이 돌아오기 전까지는 그 결과 값의 리턴을 미룬다. 응답이 들어올 때까지 기다려야만 그 값이 어떻게 될지를 알 수 있다.

다음 코드는 `fetch` API를 사용하면서 익숙하게 볼 수 있는 형태다. `fetch()`는 그 결과로 `Promise`를 리턴하는 형태로 구성되어 있다. 서버에 요청을 보내고, 서버가 그 요청을 처리한 뒤 적절한 응답을 보여줘야만 최종 결과값을 확인할 수 있는 요청이라서다.

```js
// 요청을 보내고 돌아오기 전 시점에는 console에 어떤 값이 찍힐지 알 수 없다
// 기다려봐야만 어떤 값이 언제 찍힐지 알 수 있다
// 즉, 자바스크립트는 fetch 함수 실행 후 응답을 "기다린다"
const isThereAnyone = () => {
  fetch("http://foo.com/bar/baz")
    .then((res) => res.json())
    .then((res) => console.log(res))
    .catch((err) => console.error(err))
    .finally((res) => console.log("완료", res))
}
```

그러나 Promise를 기반으로 하는 `fetch()`의 등장은 뜻밖에 생각보다 최근의 일이다. 그 이전에 자바스크립트가 서버와 동적으로 통신하기 위해서는 `XMLHttpRequest`를 사용했으며, 하나의 동작에 이어 다음 동작을 수행하고자 할 때는 xhr 인스턴스에 부착된 이벤트리스너가 사용자가 미리 정의해둔 `callback` 함수를 부르는 패턴을 주로 사용했다.

자세한 내용은 [링크](https://developer.mozilla.org/ko/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests)를 참조하자. 아래는 예시 코드다.

```js
// 출처 : MDN
function loadFile(url, callback /*, opt_arg1, opt_arg2, ... */) {
    var xhr = new XMLHttpRequest();
    xhr.callback = callback;
    xhr.arguments = Array.prototype.slice.call(arguments, 2);
    xhr.onload = xhrSuccess;
    xhr.onerror = xhrError;
    xhr.open("GET", url, true);
    xhr.send(null);
}
```

아무튼 `Promise` 객체는 비동기 작업의 성공, 실패, 요청이 진행 중인지 여부에 대한 정보를 기존의 방법보다 더 간편하게 처리하기 위해서 등장한 솔루션이다.

`Promise`는 기본적으로 다음과 같은 구조를 갖는다.

```js
const p = new Promise((resolve, reject) => {
  if (resolve condition) resolve()
  if (reject condition) reject()
})
```

`Promise` 객체는 `new` 연산자를 통해 생성된다. `Promise` 생성자는 인자로 콜백 함수를 받고, 다시 콜백 함수는 그 인자로 `resolve`와 `reject` 함수를 전달받을 수 있다. 인자로 넘어오는 함수는 각각 `Promise`의 비동기 처리가 성공/실패했음을 알리는 상태를 바꿔주는 역할을 한다.

`Promise`는 다음 세 가지 상태 값을 갖는다.
- fulfilled(resolved) : 에러 없이 promise가 잘 실행되었음.
- rejected : 해당 promise가 reject됨. catch() 등을 통해 핸들링 해야 함.
- pending : 아직 reject 되지도, resolve 되지도 않은 promise의 상태.

<br>

예시를 보자. 다음은 기존 `setTimeout` 함수를 커스텀한 예제다. 넘어오는 인자의 타입을 체크하여 숫자가 아니면 `Promise` 객체의 상태를 `reject`로 반환하고, 숫자가 맞을 경우 callback으로 받은 함수를 실행하고 `resolve`를 반환한다.

```js
const mySetTimeout = (callback, delay) => {
  return new Promise((resolve, reject) => {
    if (typeof delay !== "number") reject("not a number")

    setTimeout(() => {
      callback()
      resolve("resolved")
    }, delay)
  })
}
```

# Promise Method Chaining

자바스크립트를 사용하다보면 메서드를 연쇄적으로 연결하여 하나의 값을 다른 값으로 바꿔나가는 패턴을 흔히 확인할 수 있다. 이를 메서드 체이닝(`Method Chaining`)이라고 부른다.

```js
// 메서드 체이닝을 활용해 객체를 점차적으로 원하는 형태의 문자열로 변환
const queryToString = (queryObj) => {
  const questionMark = "?"
  const qs = Object
              .entries(queryObj)
              .flatMap(e => e.join("="))
              .join("&")  

  return questionMark + qs
}
```

앞서 보여줬던 `fetch` API 예시처럼 `Promise`로 리턴되는 값은 `.then()`, `.catch()`, `.finally()` 메서드를 연결하여 비동기적인 값을 확인하거나 매 단계마다 적절한 처리를 해줄 수 있다.

해당 메서드들은 인자로 callback 함수를 받아 다시 해당 처리의 성공/실패를 담은 새로운 `Promise` 객체를 리턴한다. 그렇기 때문에 체이닝이 가능하다. 메서드는 여러개 중첩이 가능하고, **새로운** 객체를 리턴한다는 점 또한 중요한 포인트다.

```js
mySetTimeout(() =>  console.log("wecode", 1000))
  .then((res) => console.log(res))
  .catch((err) => console.error(err))
  .finally((res) => console.log("완료", res))
```

`.then()`은 이전 `Promise`의 성공 시의 명령을, `.catch()`는 실패(에러/예외) 시의 명령을, `.finally()`는 해당 `Promise` 체인의 처리가 끝나고 마지막에 최종적으로 처리되어야 하는 명령을 처리한다.

만약 위 예제가 에러 없이 실행된다면 1, 2, 4번 라인이 실행될 것이고, 1번 줄에서 에러가 난다면 1, 3, 4번 줄 순서로 코드가 실행될 것이다. 이러한 실행 순서를 잘 파악해 메서드 체이닝을 구사하면 된다.


# Async / Await

하지만 이러한 경우를 생각해보자.

```js
fetch(url1)
  .then(res => res.json())
  .then(res => console.log(res))
  .catch(fetch(url3).then(res => res.json()))

// 동기적인 코드 여러 줄

fetch(url2)
  .then(res => res.json())
  .then(res => console.log(res))

// 동기적인 코드 여러 줄
```

비동기적인 코드와 동기적인 코드가 여러 줄 뒤섞여 있을 경우 어떤 순서로 코드가 동작하게 될지 보장할 수 없다.

또한 하나의 함수에서 모든 코드가 동기적으로 동작하기를 바랄 경우 메서드 체이닝이 끝없이 이어질 수 밖에 없을 것이고, 이는 코드의 복잡도를 부득이 증가시킨다.

이러한 문제를 해결하기 위해 제안된 문법이 `async` 함수와 `await` 키워드다. 사용법은 간단하다. `async` 함수를 선언하고 비동기 처리가 필요한 내용 앞에 `await`를 붙여주면 `Promise`를 활용한 비동기 처리를 기다렸다가 진행하게 된다. 이제 모든 코드는 동기적인 순서를 보장한다.

에러 처리에 관해서는 `try... catch` 문을 활용해줄 수 있다. ([링크](https://ko.javascript.info/try-catch))

```js
async function foo() // function 키워드를 사용할 경우
const foo = async () => { // arrow function을 사용할 경우
  try {
    const res1 = await fetch(url1)
    const json1 = await res1.json()
    console.log(json1)

    // 동기적인 코드 여러 줄

    const res2 = await fetch(url2)
    const json2 = await res2.json()
    console.log(json2)

    // 동기적인 코드 여러 줄
  } catch (err) {
    const res3 = await fetch(url3)
    const json3 = await res3.json()
  }
}
```

`async` 함수는 내부적으로 `generator` 함수와 `Promise`를 활용한 비동기 `iterator`로 구현되어 있다고 한다. 해당 문법에 대한 상세한 설명은 [링크](https://ko.javascript.info/async-iterators-generators)를 참조하자.

`redux`의 비동기 처리를 위한 미들웨어 라이브러리 `redux-saga`에서도 이러한 원리를 사용해 비동기 동작을 처리한다.