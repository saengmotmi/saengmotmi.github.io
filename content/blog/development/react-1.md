---
title: react-1
date: 2020-02-10 16:02:11
category: daily thinking
draft: false
---

## why React?

- 리액트를 활용하면 자신의 웹 애플리케이션 내 유저 인터페이스를 재사용 가능한 컴포넌트로 분리(ex. 종연님의 껍데기 html)하여 작성, 프로젝트의 유지보수를 쉽게 만들어줌

## 기본 개념 정리

**1. 리액트 프로젝트를 만들고자 하는 폴더에 들어감**

<br>

**2. create-react-app 명령을 사용해 만들어준다 => create-react-app [프로젝트명]**

<br>

**3. [프로젝트명] 안에 기본 파일들이 세팅된다**

<br>

**4. 컴포넌트(유저 인터페이스를 재사용 가능하도록 분리한 것, 일종의 UI 조각) 파일들 파헤치기**

> - ```react
>   import React, {component} from 'react';
>   // 리액트와 그 내부의 컴포넌트를 불러옴, 파일에서 JSX를 사용하려면 꼭 임포트 해줘야 함
>   ```
>
> - ```react
>   import logo from './logo.svg';
>   import './App.css';
>   // 같은 폴더 안에 있는 사진 파일과 css 파일을 불러옴 -> import는 webpack과 연관 있으나 일단 패스
>   ```
>
> - ```react
>   class App extend Component { }
>   // 클래스를 활용해 컴포넌트를 만듦. 함수로 만드는 방법도 있음.
>   ```
>
> - ```react
>   export default App;
>   // 작성한 컴포넌트를 다른 곳에서 불러와 사용할 수 있도록 내보내기 함
>   ```
>
> - **컴포넌트 사용 시**
>   // "import App from './App';" 입력하면 불러옴 <- 확장자 .js는 디폴트라 생략 가능
>   // 브라우저 상에 리액트 컴포넌트를 보여주려면 ReactDOM.Render(결과물, 타겟 DOM) 함수 사용
>   // ex. ReactDOM.render(<App />, document.getElementById('root'));
>   // 해당 파일 안에 있는 <div id="root"></div>를 찾아 보여주게 됨

<br>

**5. JSX**

얼핏 보기에는 html 같으나 자바스크립트임 -> "자바스크립트 안에 있는 html이라고 생각하자"

컴포넌트를 만들 때마다 이런 식으로 작성해야 한다면 너무나 고통스러울 것(https://i.imgur.com/SZshYmi.png)

그래서 일단 html과 비슷한 문법으로 일단 작성하면 이를(XML 형태의 코드) React.createElement를 사용하는(?) 자바스크립트 형태로 변환시키는 방식

JSX를 사용하며 주의할 문법적 사항

- div를 열면 꼭 닫아야 함(죄다 div로 묶는게 보기에 안 좋다면 <Fregment> 등을 사용)
- JSX 안에 자바스크립트 값을 사용할 때는 먼저 선언하고 { 변수 } 형태로 사용
- 렌더링에 조건을 걸어주려면 '삼항연산자'와 'AND 연산자' 사용 (if문은 불가능 -> IIFE는 가능)

```react
{
	1 + 1 === 2
		? (<div>맞아요!</div>)
		: (<div>틀려요!</div>)
}
```

```react
{
	1 + 1 === 2 && (<div>맞아요!</div>) // AND 연산자는 true일 때만 보여주고 false는 안 보여줌
}
```

- 복잡한 조건은 JSX 밖에서 작성하는 걸 권장 -> 부득이 작성해야 한다면 IIFE 사용

```react
{
	(function() {
		if (value === 1) return (<div>하나</div>);
		if (value === 2) return (<div>둘</div>);
		if (value === 3) return (<div>셋</div>);
	})()
}
```

- IIFE 대신 switch문 사용해도 무방 // 아래 코드가 switch문이라는 건 아님

```react
(() => {
	if (value === 1) return (<div>하나</div>);
	if (value === 2) return (<div>둘</div>);
	if (value === 3) return (<div>셋</div>);
})()
```

<br>

**7. style과 className**

JSX 내부 코드에 style 속성을 줄 수도 있다. 먼저 render 함수 아래에 변수에 담은 객체 형태로 스타일 내용을 작성한다. 그 다음 return 아래 들어가는 html 내부에 인라인 스타일 style 속성에 변수를 값으로 넣어준다.

html에서는 style에 들어갈 자바스크립트 코드를 텍스트로 입력했었지만 리액트에서는 객체로 넘겨주어야 한다.

```react
class App extends Component {
  render() {
    const style = {
      backgroundColor: 'black',
      padding: '16px',
      color: 'white',
      fontSize: '12px'
    };

    return (
      <div style={style}>
        hi there
      </div>
    );
  }
}
```

일부 속성의 경우 이름이 조금 달라진다. 예컨대 class는 className으로 바꿔주어야 한다.

** 8. 주석 **

주석은 `{/* */}` 혹은 `//` 스타일로 작성해주면 된다.

## 참고

- XML(Extensible Markup Language) : W3C에서 1996년 제안한 다목적 마크업(태그 등을 이용하여 데이터의 구조를 기술) 언어. 가장 친숙하고 흔하게 접할 수 있는 마크업 언어로 HTML이 있다.

  - XML을 이용하면 어디부터 어디까지가 데이터 이름이고 어디부터 어디까지가 실제 데이터이며 어디부터 어디까지가 데이터 단위인지도 표현이 가능하다. 즉, 데이터에 의미를 부여하는 메타데이터를 기술할 수 있다.

- IIFE(Immediately Invoked Function Expression) : 정의되자마자 즉시 실행되는 자바스크립트 함수

  - https://developer.mozilla.org/ko/docs/Glossary/IIFE (mozilla 링크 참고)
  - 문법 : (function () { statements })();
    - 두 개의 괄호() 영역이 있음 1) 괄호로 둘러싸인 익명함수 2) 즉시 실행 함수를 생성하는 괄호
  - 표현 내부의 변수는 외부로부터 접근 불가능 + 변수에 할당시 IIFE 자체는 저장되지 않고 실행 결과만 저장

- 원본 내용 블로그 : https://velopert.com/3626
