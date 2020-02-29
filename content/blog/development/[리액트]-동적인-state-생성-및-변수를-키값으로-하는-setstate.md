---
title: [리액트] 동적인 state 생성 및 변수를 키값으로 하는 setState
date: 2020-03-01 01:03:96
category: development
draft: false
---

마켓컬리 클론 코딩 1차 프로젝트를 하던 중 setState() 활용에 대한 조그만 의문점이 생겼다. 왜 state를 정적으로만 사용하지? 필요에 따라 동적으로 state를 생성하고 관리할 수는 없느냐는 말이다.

물론 가능하다. setState()의 형태를 생각해보면 안에는 객체가 들어가고, 즉 객체를 사용하는 방식으로 사용하면 된다.

객체에 접근하는 방식은 `.key`와 `['key']`가 있다. 전자의 경우 static한 키 값의 경우 많이 사용되고 후자는 키 값에 띄어쓰기가 있거나 변수로 전달해야 할 때 사용한다. 후자를 사용해보기로 했다. 다음과 같은 활용이 가능하다.

```jsx
<button
  onClick={() => {
    for (let i = 0; i < 10; i++) {
      this.setState(
        {
          [i % 5]: this.state[i % 5] ? this.state[i % 5] + 1 : 1,
        },
        () => {
          console.log(i, i % 5, this.state)
        }
      )
    }
  }}
>
  test
</button>
```

test라는 버튼을 만들고 그 버튼을 누르면 0~4까지 키값을 갖는 state를 생성한다. 없는 키값에 접근하면 undefined가 발생하므로 삼항연산자를 활용해 false 발생시(undefined는 boolean 값으로는 0이다) 1을 부여하고 이후 처리부터는 1씩 더해준다.

루프는 총 10회 돌고 키값은 나머지 연산을 이용해 0~4까지만 값을 갖도록 했다. 따라서 초기 5회는 0~4까지의 키를 생성하고 각각의 value에는 1을 부여한다. 그 뒤 5회는 값을 1씩 더하므로 모두 2를 value로 갖는다.

버튼을 한번 더 누르면 value는 3, 4로 올라가게 될 것이다.
