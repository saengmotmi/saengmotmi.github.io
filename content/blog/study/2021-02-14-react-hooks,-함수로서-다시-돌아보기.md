---
title: 2021-02-14 React Hooks, 함수로서 다시 돌아보기
date: 2021-02-14 16:02:14
category: study
draft: false
---

함수 컴포넌트의 Hooks는 클래스형 컴포넌트의 기능들 대부분을 구현하고 있기에 두 개념에 대해 유사한 멘탈 모델을 가지고 접근할 수도 있다. 그러나 함수 컴포넌트와 Hook의 실질적인 동작을 이해하기 위해선 그것의 배경이 되는 함수라는 컨셉을 천천히 곱씹어볼 필요가 있다.

물론 완전한 설명까지는 될 수 없고, 이번 문서에서는 그 "함수라는 컨셉"을 조금 체감해볼 수 있는 의문들을 조금씩 꿰어보는 것이 목적이다.

더 정확하고 자세한 설명은 포스팅 가장 하단에 걸려 있는 "useEffect 완벽 가이드"와 "Getting Closure on React Hooks"를 참조하면 좋다. CRA, Redux를 만든 Dan Abramov의 글인데, 아직까지 저 글보다 Hooks에 대하여 명쾌하게 설명해놓은 글을 읽어본 적이 없다!

## 0. 시작하기 전에...

나는 워낙 갓길로 새는 걸 좋아하니까 시작 전 잠시 옆 길로 새보자...

### 1) 함수 '컴포넌트' 아니고 '함수' 컴포넌트

hooks를 어느 정도 사용해보았다는 전제 하에 `useEffect()`, `useState()` 를 조금 낮설게 보아도 좋겠다. 함수 `컴포넌트`보다는 `함수` 컴포넌트라는 관점에서 useEffect를 바라보자.

```jsx
// 이렇게요...
const FunctionComponent = (props) => JSX
```

개인적으로는 처음 함수 컴포넌트를 접했을 때 그 형태를 자체를 외웠기 때문에 이 컴포넌트가 함수라는 생각 자체를 해본 적이 없었다. useEffect의 동작 역시 그냥 그렇게 생겼으니까 그렇게 되는구나, 정도로 생각했었다.

하지만 함수라는 관점에서 다시 바라보자. 그러면 그냥 그렇게 생겼던 것들이 조금 새롭게 보이기 시작한다.

```jsx
const Main = () => { // 1
	const [state, setState] = useState() // 2-1
	useEffect(() => {}) // 2-2, 4

	return (something) // 3
}
```

- 1) `Main`이라는 함수가 실행된다. Main 내부의 코드들이 위에서부터 한줄 씩 처리된다.
- 2-1) `useState()` 훅이 실행된다. 실행 결과가 배열로 리턴된 뒤, 첫번째 요소와 두번째 요소가 각각 `state`, `setState`에 담긴다.
- 2-2) `useEffect()` 훅이 실행된다. useEffect의 첫 번째 인자에 담겨 있는 callback 함수는 실행될 적당한 시기를 기다렸다가 추후에 실행된다. (일단 '적당한'에 대해서는 넘어가자)
- 3) return에 담긴 로직과 JSX 요소들이 처리된다.
- 4) useEffect에 담긴 callback 함수가 처리된다.

### 2) 클로저, 클로저, 클로저

자바스크립트 기본 문법 만지던 때는 클로저의 필요성은 커녕 대체 왜 이런게 있어야 하는지 모르겠는 개념이었을 거라고 자신 있게 얘기할 수 있다. 내가 그랬다... (심지어 이건 그냥 자바스크립트 특유의 버그가 아닐까 하는 생각까지도 했었다.)

하지만 당연히 그렇지 않고 매우 매우 매우 매우 중요한 개념이고, 함수라는 개념 자체를 바라볼 때 특히 클로저는 빼놓을 수 없다.

React Hooks를 다루고 있는 포스트인 만큼 주제와 연관된 예제를 들어보자.

```jsx
// 아주 간단한 버전의 useState. 실제 코드와는 매우 다릅니다.
const useState = (init = undefined) => {
  let value = init

  const getter = () => value // 클로저
  const setter = next => (value = next) // 클로저

  return [getter, setter]
}

const [state, setState] = useState('클로저')

state()
setState("어려워!")
state()
```

클로저를 아주 간단하게 퉁쳐서 설명하자면 **"자신이 생성될 시점의 환경을 기억하는 함수"**다. 여기서 이야기하는 "환경"이라는 건 그리 대단한 것은 아니고, 일단 자신을 감싸고 있는 스코프에 담긴 변수들이라고 보아도 무방하겠다.

위 예제에서는 `getter` 와 `setter` 함수 입장에서 변수 `value`를 바라보는 것과 같다. 이 두 클로저가 배열에 담겨 외부로 노출되고, 새롭게 `state`, `setState`라는 변수에 할당되었다.

이 두 변수에 담긴 함수들은 자신이 생성될 당시의 환경을 기억하고 있으므로 실행 될 때마다 자신의 외부에 정의된 변수 `value`를 참조할 수 있게 된다. 여기서 "자신이 생성될 당시의 환경"을 기억한다는 사실이 매우 중요하다.

이제 이러한 배경을 가지고 다음 두 가지 문제에 접근해보자.

- useEffect의 return 안에서 사용된 state 등의 상태값들이 이전 렌더링의 값을 바라봄
- useEffect 내부에서 사용되는 함수를 의존성 배열에 포함시킬 경우 ESLint가 useCallback을 강제함

## 1. "왜 자꾸 Undefined 가 찍히죠...?"

얼마 전 소제목과 같은 질문을 받았다. console을 찍어보니 `useEffect` 안에서 호출된 함수가 다루는 값들이 예전 버전의 값을 바라보고 있는 문제였다.

비동기 상황의 흔한 케이스라고 할 수 있다. useEffect 입장에서는 당연히 외부 값을 관찰하지 못한다. 즉 의존성 배열이 비어 있는 `useEffect`, 조금 더 정확히는 useEffect의 첫 번째 인자로 넘겨진 콜백 함수 안에서라면 page는 `undefined` 만을 반영할 것이다.

```jsx
import { useState, useEffect } from "react"

const Main = () => {
	const [page, setPage] = useState()
	const [data, setData] = useState()

	// getSettings 내부에서 사용되고 있는 data는
	// 업데이트 된 이후의 data를 가져올 수 없기 때문에
	// page가 변경되더라도 data는 undefined를 출력
	const getSettings = () => {
		fetch("url" + "/" + page, {
			method: "post"
			body: JSON.stringify(data)
		})
	}

	useEffect(() => {
		if (!data) return;
		getSettings()
	}, [page])

	useEffect(() => {
		fetch("url")
			.then(() => {})
			.then((res) => setData(res))
	}, [])

	return <button onClick={() => setPage(대강 1~9의 숫자)} />
}
```

그러나 이것을 "의존성 배열은 바깥의 값을 바라보는 창구", "빈 배열이 들어간 useEffect는 CDM과 같다" 정도의 비유로만 묻어두고 넘어가게 되면 도저히 이해할 수 없는 현상이다.

서두에서 말했던 배경을 염두에 두고 useEffect가 처음 실행 되던 당시의 값들을 추적하며 코드를 한 줄 씩 체크해보자.

- **1번 useEffect가 실행된다.** 1번 useEffect의 첫 번째 인자에 담긴 callback 함수가 생성되고 '자신의 환경을 기억한 채로' 적당한 시기에 실행되기 위해 대기실로 옮겨간다.
  - 이 때 callback 함수가 기억하게 되는 값 중 실제 참조하게 되는 변수는 data(undefined), page(undefined)이다.
- **2번 useEffect가 실행된다.** 2번 useEffect의 첫 번째 인자에 담긴 callback 함수가 생성되고 '자신의 환경을 기억한 채로' 적당한 시기에 실행되기 위해 대기실로 옮겨간다.
  - 이 때 callback 함수가 기억하게 되는 값 중 실제 참조하게 되는 변수는 없다.
- **render 안의 로직과 JSX 요소들이 처리된다.**
- **1번, 2번 useEffect 안에 담겼던 callback 함수들이 실행된다.**
- **1번 callback에 담겨있던 `getSettings()` 가 실행되지만, 여기서 참조하는 변수 `page`와 `data` 는 undefined 이므로 에러가 발생한다.**

<br>

이러한 현상은 함수 컴포넌트가 함수라는 사실과, 함수라는 점을 활용해 클로저를 적극 사용하고 있다는 사실을 이해하고 나면 받아들일 수 있는 결과다.

사실 애초에 hook 이름 부터가 `useEffect`다. 이 이름은 다분히 **순수 함수**라는 개념을 염두에 두고 네이밍 된 것으로 보이는데, 여기서 순수 함수란 side effect(함수 외부 세계에 영향을 미치는 동작)가 없는 함수를 뜻한다.

함수 외부에 정의된 변수를 건드리는 것은 물론이고, console.log()를 활용해 변수를 콘솔에 프린트 하는 것 조차도 함수 외부의 요소와 상호작용 하는 것으로 볼 수 있다. (순수 함수를 활용하면 값을 안전하게 다룰 수 있으므로 함수형 프로그래밍에서는 최대한 함수의 인자로 전달된 요소들과 그것들의 연산 결과인 리턴값들의 체이닝으로 값을 도출하려 한다.)

그렇기 때문에 useEffect는 함수 컴포넌트 외부에 영향을 미치는 동작들, 예컨대 data fetching이나, dom에 대한 조작, 다음 번 렌더에 영향을 미칠 때(clean up) 등 side effect를 일으키기 적당한 자리를 제공하기 위해 설계되었다고 볼 수 있다.

```js
const Foo = () => {
  const [state, setState] = useState(0);

  console.log("render", state);

  useEffect(() => {
    console.log("useEffect Callback", state);
    return () => console.log("cleanUp", state);
  }, [state]);

  return <div onClick={() => setState(state + 1)}>하잉</div>;
};

export default Foo;
```

```js
render, 0
useEffect Callback, 0

// 클릭

render, 1
 // highlight-next-line
cleanUp, 0 // ???
useEffect Callback, 1
```

아직 useEffect의 정확한 동작 순서와 값을 반영하는 타이밍이 어색하다면, "useEffect 완벽 가이드"를 읽으며 useEffect callback의 return 자리에 담기는 clean up 함수의 실행 타이밍과 그 함수가 반영하는 값의 상태가 어떠한지 조금 더 따져보는 시간을 가져보면 좋겠다.


## 2. useCallback with Dependency Array

두 번째 경우도 살펴보자. useCallback은 인자 두개를 받는 Hook으로, 첫 번째 인자에는 콜백 함수를 받고 두 번째 인자에는 첫번째 인자에 전달된 콜백 함수를 언제 새롭게 리턴할 것인지 결정하는 의존성 배열이 들어간다.

```jsx
const memoizedFunction = useCallback(Function, [foo])
```

위와 같은 구조로 사용되는데, useCallback이 리턴하는 값은 첫 번째 인자에 전달된 함수가 메모이제이션 된 버전의 함수 인스턴스다. 즉, 의존성 배열에 전달된 값에 변화가 생길 때에만 새로운 함수 인스턴스가 리턴된다.

이를 앞서 언급한 useEffect의 의존성 배열과 함께 살펴보자.

```jsx
const Main = () => {
  //highlight-next-line
	const getData = () => {} // 매 렌더링 마다 새로운 함수 인스턴스가 생성됨

	useEffect(() => {
  //highlight-next-line
		getData()
  //highlight-next-line
	}, [getData]) // 따라서 매 렌더마다 업데이트 됨
}
```

함수 컴포넌트는 컴포넌트이기 이전에 "함수"다. 따라서 Main이라는 컴포넌트이자 함수가 불려나오면 상단에서부터 한 줄씩 자바스크립트 코드가 실행된다.

useEffect는 의존성 배열 안에 담긴 값들의 이전 렌더링과 이후 렌더링 값을 비교하여 첫 번째 인자에 전달 받은 콜백 함수를 실행한다고 했다. 위의 예시 코드의 경우 getData가 새로운 값으로 바뀌었을 때다.

`getData` 의 경우를 보자. 드라이하게 생각했을 때 `const getData =` 부분은 우변의 값을 새롭게 getData라는 변수에 할당하겠다는 뜻이다. 그리고 우변에서는 화살표 함수의 인스턴스가 새롭게 생성되고 있다. getData는 이전 렌더의 결과와 무관하게 새로운 주소 값이 할당된다.

그렇다면, useEffect 의존성 배열에는 매번 새로운 값이 전달되고 매번 새로운 값을 참조하여 useEffect의 callback 함수가 호출된다. 그렇기 때문에 ESLint가 useCallback의 사용을 강제하는 것이다.

그건 우리가 원하는 동작이 아니다. getData에 한번 값을 지정하고 나면 주소 값이 바뀌지 않았으면 좋겠다.

그럴 때 useMemo, useCallback 등의 메모이제이션 훅을 사용하면 된다.

```jsx
const Main = () => {
  //highlight-next-line
	const memoGetData = useCallback(() => {}, []) // 첫 렌더 때 저장된 인스턴스로 고정됨

	useEffect(() => {
  //highlight-next-line
		memoGetData()
  //highlight-next-line
	}, [memoGetData]) // 첫 렌더 이후 실행되지 않음
}
```

## 3. 함수 컴포넌트 최상위에 Hook을 정의하는 이유?

Hook이 함수 최상위에 선언되어야 하고, 조건문에 들어가면 안되는 이유 또한 함수 컴포넌트가 "함수"이기 때문에 갖게 되는 특성을 해결하는 과정에서 발생한 조건이다.

함수는 한번 실행되고 나서 더 이상 자신을 참조하는 대상이 없다면 가비지 컬렉터에 의해 정리되고, 메모리 상에서 자신의 정보를 잃게 된다.

그렇다면 `useState()` 가 어떻게 상태값을 저장할 수 있을까라는 질문에 대해 던져봤던 질문을 동일하게 던져볼 수 있다. "현재의 상태가 사라진다면 어떻게 다음 렌더와의 상태를 비교할 수 있을까?"

역시 대답은 클로저다. 함수 컴포넌트 외부에 정의된 특별한 변수에 해당 컴포넌트에 대한 정보를 담아두고 다음 렌더 때 이 변수를 참조하도록 하면 된다.

```jsx
// Main.js
const Main = () => {
  if (something) { //highlight-line
    const [a, b] = useState() //highlight-line
  } //highlight-line
	const [c, d] = useState()

	useEffect(() => {})

	return <></>
}

// 렌더 버전 1
[
	"0": type: useState, data: [a, b],
	"1": type: useState, data: [c, d],
	"2": type: useEffect, data: () => {}
]

// 렌더 버전 2
[
	"0": type: useState, data: [a, b], <- ??? //highlight-line
	"1": type: useEffect, data: () => {}
  "2": <- ??? //highlight-line
]
```

그런데 이 정보를 담아두는 자료 형태가 배열이다. 배열은 순서를 나타내는 인덱스 말고는 각각의 정보들을 구분하는 기준이 없다. 따라서 이 순서가 뒤섞이지 않을 필요가 있다. 만약 첫 번째 useState가 어떠한 이유로 불려오지 않는다면 c, d의 데이터를 a, b처럼 착각하게 될 것이다.

간단히 결론만 말하자면 **1) 해당 hook에 대한 정보를 매 렌더 때마다 체크할 수 있어야 하고, 2) 렌더 간 Hook에 대한 정보를 일관되게 유지하기 위해서** Hook은 함수 컴포넌트 최상위에 정의되어야 한다. (함수 body 부분이 render 그 자체이기 때문에)

<br>

### 참고 자료
--------------------------------

- [[번역] useEffect 완벽 가이드](https://rinae.dev/posts/a-complete-guide-to-useeffect-ko)
- ['Getting Closure on React Hooks' 정리](https://rinae.dev/posts/getting-closure-on-react-hooks-summary)


`#WeCode` `#위코드`