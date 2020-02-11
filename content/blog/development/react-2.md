---
title: react-2
date: 2020-02-11 11:02:41
category: development
draft: false
---

### 리액트님, 죄송한데 도저히 이해가 안됩니다...

지난 2주 동안 html, css, 자바스크립트에 순서대로 입문해오면서 느낀 벽은 솔직히 그렇게 까진 높지 않았다. 물론 기본적인 컨셉에 대한 학습이 주된 내용었다는 점이 가장 큰 이유였을 것이다.

하지만 지난 학습 내용들이 내가 가지고 있던 프로그래밍에 대한 기본 컨셉을 크게 벗어나지 않았던 이유도 학습속도에 적잖은 영향을 미쳤을 것이다. 굳이 겪었던 어려움을 꼽아보자면 js의 클래스 개념과 ES6의 애로우 펑션 정도?

그러나 리액트는 왜 써야 하는지는 물론이고 당장 hello world를 띄우는 절차조차 어색했다. 문법은 html과 자바스크립트의 혼종인 JSX(JavaScript XML)를 사용했을 뿐더러, html 및 js파일들은 물론 각종 변수들이 어지럽게 얽혀 나의 인지과부하에 큰 공헌을 했다.

### 그래도 두드리면 (아주 조금은) 열리더라

그래도 만인의 선생님 고잉이형을 믿고 차근차근 코드를 따라 쳐보기로 했다. 다행히 '이게 뭔소리여' 정도는 벗어날 수 있었다. 이번 포스트에서는 내가 간신히 이해한 내용들을 조악하게나마 내가 이해한 방식으로 정리해보기로 한다.

### 0. 리액트... 도당채 뭐세요?

우선 [위키피디아](https://ko.wikipedia.org/wiki/%EB%A6%AC%EC%95%A1%ED%8A%B8_(%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC))에 따르면 리액트는,

> 컴퓨팅에서 리액트(React, React.js 또는 ReactJS)는 자바스크립트 라이브러리의 하나로서사용자 인터페이스를 만들기 위해 사용된다. 페이스북과 개별 개발자 및 기업들 공동체에 의해 유지보수된다.

1 뭔소리여가 추가됐다. 예리님 언급을 생각해보자면 리액트는 jQuery와 같은 '라이브러리'다. 다시 라이브러리란 무엇이냐 묻는다면 [링크](https://blog.gaerae.com/2016/11/what-is-library-and-framework-and-architecture-and-platform.html)의 자동차 비유를 참조해 대답하겠다.

![자동차 바퀴](https://3.bp.blogspot.com/-Q7rxJuGwL40/WD7SK67gGTI/AAAAAAAAB58/kMDlk_UEl5UO1hKk_dOSP_LZVMfWRdQiwCLcB/s320/what-is-library.jpg "자동차 바퀴")
> **간략 설명 : 프로그램 제작 시 필요한 기능**
> 
> **비교 설명 : 자동차 바퀴, 자동차 헤드라이트, 자동차 에어백**
> 
> 재사용이 필요한 기능으로 반복적인 코드 작성을 없애기 위해 언제든지 필요한 곳에서 호출하여 사용할 수 있도록 Class나 Function으로 만들어진 것입니다.
> 
> 사용 여부는 코드 작성자 선택 사항이며 새로운 라이브러리 제작 시에도 엄격한 규칙이 존재하지 않습니다. 제작 의도에 맞게 작성하면 됩니다.

감이 올듯 말듯 하니 [하나 더](https://shrtorznzl.tistory.com/45) 보자.

![라이브러리](https://t1.daumcdn.net/cfile/tistory/9963D63B5B8755E124 "라이브러리")
> Java에서는 우리가 원하는 모든것은 객체 즉, class 단위로 작성하여 프로그램을 작성한다. 우리가 작성하는 class외에 Java에서는 미리 자주 사용되고 유용한 기능을 하는 class들을 만들어 놓고 제공을한다. 이떄 이 클래스들을 관련된 기능별로 분류하여 패키지단위로 묶어서 관리하는데 이 패키지 파일을 라이브러리라고 한다. API는 이러한 클래스들의 코드를 문서로 제공하는 것이다.

'단위 묶음', '자주 사용' 등이 중요한 키워드 인듯 하다. 일단은 '자주 사용'되는 기능들의 '단위 묶음'인 클래스 등을 잘 분류해두고 필요할 때 마다 사용하여 코드 작성의 생산성을 높이는 툴인건 알겠다.

그리고 이 툴이 MVC(Model, View, Controller) 중 특히 디자인 작업에 주로 사용된다는 사실 또한 덧붙여 두자.

### 1. 시작은 '엔트리 포인트'부터

당연한 얘기다. 엔트리 포인트라는 말 자체가 '시작 점'이니까 말이다. 그러니 무엇을 시작할 건지를 생각해보아야 한다.

리액트는 확장된 자바스크립트 문법에 기반한 라이브러리다. 별도의 설치 과정과 세팅이 필요하나 'Create React App'을 이요하기로 하자. [공식 페이지](https://reactjs-kr.firebaseapp.com/docs/installation.html)의 안내에 따르면 설치와 프로젝트 세팅을 위해선 다음 명령어를 터미널에 차례로 입력해야 한다.

```bash
npm install -g create-react-app
##혹은 npx create-react-app my-app를 실행
create-react-app my-app

cd my-app
npm start
```

이렇게 프로젝트가 최초 세팅되고 로컬 서버가 열리면 public 폴더 아래 index.html, index.js, index.css부터 만지기 시작하면 된다. 이는 Create React App에서 정해놓은 사항이니까 그냥 그렇게 알고 있자.

### 2. ```ReactDOM.render(<App />, document.getElementById('root'));```

index.html에는 ```<div id="root"></div>```라는 내용이, index.js에는 ```ReactDOM.render(<App />, document.getElementById('root'));```라는 내용이 있다. 최상단을 보면 ```import App from './App'```이 코드의 뜻은 다음과 같다.

- index.html에 **root라는 id를 가진 div 태그**가 있는데,
- index.js에서는 React를 이용해 해당 태그에 **```<App>```이라는 사용자 정의 태그**를 실행 시킬 거고,
- ```<App>```이라는 사용자 정의 태그의 내용은 이 파일들과 같은 경로에 있는 'App.js'에 정의되어 있다. 코드를 쭉쭉 실행시키고 컴포넌트 안에 있는 render() 함수를 통해 최종적으로 사용자 정의 태그를 실행시키게 된다.

고잉이형의 강의 내용에 따르면 컴포넌트는 **'리액트를 통해 만든 사용자 정의 태그'** 이며 JSX 문법을 통해 작성하도록 되어 있다. 아래 코드는 컴포넌트의 예시다.

```jsx
import React, { Component } from 'react';
import './App.css';

class Content extends Component {
  render() {
    return (
      <article>
        <h2>{this.props.title}</h2>
        {this.props.desc}
      </article>
    );
  }
}
```

JSX에서 지켜야 할 몇 가지 문법 사항은 다음과 같다.

1) 컴포넌트는 최상위 태그 하나로 닫아줘야 한다.

```jsx
render() {
    return () {
        <div>
            <HelloWorld />
        </div>
    } // 빈 엘리먼트의 경우 XML처럼 < />로 닫아줘야 한다
}
```

2) JSX 안에 자바스크립트 값을 사용할 때는 먼저 선언하고 { 변수 } 형태로 사용

```jsx
<h1>
    hello react, i'm {name}.
</h1>
```

3) CSS 속성 중 하이픈('-')이 들어간 경우 속성명을 카멜 케이스(camelCase)로 작성해야 한다. 

4) 조건부 처리 = 삼항 연산자 & 논리 연산자(AND, OR)

```jsx
return() {
    <div>
        { true && true
            ? <h1>참이에용</h1>
            : <h1>구라에용</h1>
        }
    </div>
}
```

```jsx
return() {
    <div>
        { true && <h1>AND에용</h1> }
        { false || <h1>OR에용</h1> }
    </div>
}
```

물론 이게 전부는 아니다. 나머지는 리액트를 사용하며 차근차근 익혀나가자.

### 3. props & state

리액트에서 컴포넌트와 컴포넌트 사이에서 데이터를 주고 받을 때 **props**와 **state** 두 가지를 사용한다.

- props : 부모 컴포넌트가 자식 컴포넌트에게 주는 값
  
  (자식은 부모에게서 받을 수만 있고 받은 props를 수정할 수 없음)
- state : 컴포넌트 내부에서 선언하며 내부에서 값을 변경할 수 있음

받아온 props 값은 `this.` 키워드를 사용해 확인할 수 있으며, 다음과 같이 객체 형태로 데이터를 전달할 수 있다.

```jsx
class App extends Component {
//App이라는 class를 만들고 Component라는 리액트가 가지고 있는 클래스를 상속해서
//새로운 클래스를 만들고 render라는 메소드를 만드는거다
//얘도 마찬가지로 컴포넌트니까 태그로 감싸줘야 함
//this와 props를 사용해서 개쩌는 걸 할 수 있음 ㄹㅇ

  constructor(props) { //constructor가 존재한다면 render보다 먼저 실행되면서 초기값 설정
    super(props); // state의 값을 초기화, 근데 constructor, super 필요 없다고도 하던데
    
    this.state = {
      mode: 'read',
      subject: {title:'WEB', sub:'World Wide Web!'},
      welcome: {title:'welcome', desc:'hello, react!'},
      contents: [
        {id:1, title:'HTML', desc: 'HTML is for information'},
        {id:2, title:'CSS', desc:'CSS is for design'},
        {id:3, title:'JavaScript', desc:'JavaScript is for interactive'},
      ]
    }
  }

  render() {
    let _title, _desc = null;

    if (this.state.mode === 'welcome') {
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
    } else if (this.state.mode === 'read') {
      _title = this.state.contents[0].title;
      _desc = this.state.contents[0].desc;
    }
    return (
      <div className="App">
        <Subject
          title={this.state.subject.title}
          sub={this.state.subject.sub}>
          </Subject>
        <Subject title="react" sub="for UI"></Subject>
        <TOC data={this.state.contents}></TOC>
        <Content title={_title} desc={_desc}></Content>
      </div>
      
    );
  }
}

export default App;
```



## 참고

- 원본 내용 블로그 : https://velopert.com/3629
- 참고한 강의 : [생활코딩 리액트](https://www.youtube.com/playlist?list=PLuHgQVnccGMCRv6f8H9K5Xwsdyg4sFSdi)