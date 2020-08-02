---
title: 2020-08-03 useEffect Hook
date: 2020-08-03 01:08:08
category: study
draft: true
---

useEffect Hook의 모양을 열심히 째려보자.

```js
useEffect(() => {
  // function
  return (
    // clean up function
  )
}, [dependency])
```

오케이. 다 째려본 것 같다.

이제부터 하나씩 뜯어보자.

## 부수 효과 함수는 렌더링 결과가 실제 돔에 반영된 후에 비동기로 호출된다

Hooks라는 편견을 걷어내고 살펴보자. useEffect라는 함수가 있다. 첫 번째 인자로 함수를 받는다. 내부 구현이 어떻게 되어 있는지는 모르지만 이 함수는 렌더링 결과가 실제 돔에 반영된 후(`componentDidMount`) 호출되며, 컴포넌트가 사라지기 직전에 마지막으로 호출(`componentWillUnmount`)된다.
