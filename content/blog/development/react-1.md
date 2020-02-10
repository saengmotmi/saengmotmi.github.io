---
title: react-1
date: 2020-02-10 16:02:11
category: daily thinking
draft: false
---

## why React?

- 리액트를 활용하면 자신의 웹 애플리케이션 내 유저 인터페이스를 재사용 가능한 컴포넌트로 분리(ex. 종연님의 껍데기 html)하여 작성, 프로젝트의 유지보수를 쉽게 만들어줌



## 기본 개념 정리


0. **리액트 프로젝트를 만들고자 하는 폴더에 들어감**



1. **create-react-app 명령을 사용해 만들어준다 => create-react-app [프로젝트명]**



2. **[프로젝트명] 안에 기본 파일들이 세팅된다**



3. **컴포넌트(유저 인터페이스를 재사용 가능하도록 분리한 것, 일종의 UI 조각) 파일들 파헤치기**

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
   >

4. **JSX**

- 얼핏 보기에는 html 같으나 자바스크립트임 -> "자바스크립트 안에 있는 html이라고 생각하자"

- 컴포넌트를 만들 때마다 이런 식으로 작성해야 한다면 너무나 고통스러울 것(https://i.imgur.com/SZshYmi.png)

- 그래서 일단 html과 비슷한 문법으로 일단 작성하면 이를(XML 형태의 코드) React.createElement를 사용하는(?) 자바스크립트 형태로 변환시키는 방식

- JSX를 사용하며 주의할 문법적 사항

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

  

* XML(Extensible Markup Language) : W3C에서 1996년 제안한 다목적 마크업(태그 등을 이용하여 데이터의 구조를 기술) 언어. 가장 친숙하고 흔하게 접할 수 있는 마크업 언어로 HTML이 있다.

  * XML을 이용하면 어디부터 어디까지가 데이터 이름이고 어디부터 어디까지가 실제 데이터이며 어디부터 어디까지가 데이터 단위인지도 표현이 가능하다. 즉, 데이터에 의미를 부여하는 메타데이터를 기술할 수 있다.

* IIFE(Immediately Invoked Function Expression) : 정의되자마자 즉시 실행되는 자바스크립트 함수

  * https://developer.mozilla.org/ko/docs/Glossary/IIFE (mozilla 링크 참고)
  * 문법 : (function () { statements })();
    * 두 개의 괄호() 영역이 있음 1) 괄호로 둘러싸인 익명함수 2) 즉시 실행 함수를 생성하는 괄호
  * 표현 내부의 변수는 외부로부터 접근 불가능 + 변수에 할당시 IIFE 자체는 저장되지 않고 실행 결과만 저장
  
  

5. **style과 className**

- style 속성을 주려면 render 함수 아래에 객체 형태로 내용을 작성하고 return 안쪽 html 내부에 style 속성으로





component : 리액트에서 사용하는 UI 단위들을 컴포넌트라고 하는거고

state : 컴포넌트에서 변경 가능한 상태 -> 뭐를 state로 관리할지 설정해야 함

set



영상보기
영어버전(권장): https://www.youtube.com/watch?v=sBws8MSXN7A
한국어버전: https://opentutorials.org/module/4058

2. react글 1~5(이벤트까지) 읽기 (여기서 입장질문 나옴)
   https://yeri-kim.github.io/posts/react-jsx/
3. 더 공부하고 싶으면
   https://ko.reactjs.org/tutorial/tutorial.html
   에서 tic tae toe 게임 만들기



김현준 / 권기현 / 김승준



```
ㅁㄴㅇㄹ
ㅁㄴㅇㄹ

```

ㅁㄴㅇㄻㄴㅇㄹㄴㅇㄹ ```ㅁㄴㄹ``` ㅁㄴㅇㄻㄴㅇㄹ





리액트 왜 쓰지? => 면접에서 물어봄

jQuery -> 라이브러리 = 메서드들의 모음

- dom 접근할 때 엄청 길어지고 어떤 이벤트 할지 알아야 하고
- 엄청 쉽게 dom 접근해서 수정하고 이벤트 추가하고 엄청 쉬움
- 한 파일 내에서 전부 수정하기 쉬움
- 프론트엔드에서 가장 많이 하는게 api 호출해서 받아온 데이터들을 넣어서 html 수정하는 거 => state라는 걸로 혁명적으로 수정하기 쉽게 만들어 줌
- 컴포넌트가 빈 배열 -> 컴포넌트에 접속해서 dom에 접근하지 않고도 state로 데이터 관리
- 생산성 높아짐
- vue랑 angular도 비슷함. vue도 많이 씀. 리액트 60, vue 30, 나머지 10
- reactNative 등장하면서 사용자 더 많이 일어남
- vue VS react 뭐가 좋냐 비교 많이 했었음 but 그래봤자 0.0001초 차이임 => 생태계가 중요



create react로 인스타그램까지

html css 복붙해서 손 안볼 수 있도록 하자

실무 들어가면 한글로 구글링할 일 없음



리액트도 라이브러리 -> 갖다 쓸 수 있게 클래스 제공 + 자바스크립트로 바꿔줘야 함 + 웹팩 & 바벨 등 공부해야 함(나중에 공부하는게 좋음) = 환경 세팅할게 너무 많음

이제부터는 통신을 해야 함 -> 백엔드 api 호출해야 하고, 이미지 가져와야 하고 -> 백엔드 간단하게 배워서 서버 간단하게 띄워야 함 -> 간단한 명령어로 개발 서버 띄워줘, 수정하면 리로드 해줘, js 코드 수정해줘 => 알아서 다 해준다

단점은 세팅을 수정하기 힘듬 => 환경설정 fixed (ex. 탈옥) // 실무에서도 많이 씀

create-react-app도 명령어임 => 폴더 만들고 폴더명 수정해도 문제 x

npx는 npm 기반 명령어 -> 한번 설치하고 사라짐, 저장공간 사용 x

- https://geonlee.tistory.com/32



package.json : 완전 중요 => 모든 프로젝트마다 하나씩 꼭 존재

- 리액트도 라이브러리

- 프로젝트마다 라이브러리 진짜 많이 사용

- 자동 리로드, jsx js로 바꿔줌 등 다 인스톨 해서 프로젝트마다 세팅을 해야 됨 -> 그 모든 정보가 package.json의 dependencies에 들어 있음 => 버전만 관리해 주면 됨 => 실제 node_modules에 파일들이 들어 있는데 용량이 큼 => 깃허브가 안좋아하겠죠?  => 실제 파일은 로컬에만 저장해두고 있음

  - .gitignore에 깃허브에 안올릴 걸 설정해두면 안올라감

- 실제 소스코드 작성은 src에서 함 -> 용량은 엄청 작음 but 패키지 용량이 엄청 큼

- 패키지.제이슨 밑에 있는 디펜던시 안에 있는 버전정보만 확인해서 알아서 설치

- yarn도 npm을 감싸고 있음 혼용해도 ㄱㅊ

- 급하고 안정적으로 개발을 하려면 라이브러리를 많이 사용하게 됨

  npm(라이브러리의 앱스토어)으로 다 관리함 => 이름으로 설치할 수 있음

- 사용한 라이브러리가 디펜던시에 자동으로 추가 되지 않기 때문에 "npm install axios --save" 이용해서 자동으로 추가하도록 세팅



엔트리포인트



태그는 태그로 닫아야 함

셀프 클로징 태그도 꼭 닫아야 함



내일 라우트까지 공부해오기

- 페이지 이동이 이해 되면 메인 페이지까지 복붙



전부 클래스형 컴포넌트로 만들자

- 리액트에서는 라이프사이클 매우 중요함



class Button extents Componet { }

```<Button />``` <- 인스턴스화 한 것을 실제 페이지에서 사용

리액트는 this 안에 본인에 대한 정보를 엄청 많이 가지고 있음 => 사이트에 부하가 걸리니까 함수형 권장 => 근데 아직 함수형 컴포넌트 사용이 익숙지 않아서 넘어오는데 시간 걸릴 것

- 웬만하면 class 쓰지마 라고 함 => 함수형 컴포넌트 등장 => 클래스 형에서 쓸 수 없는 메소드 보완할 수 있는 hooks 등장
- 무조건 일단 class 형으로 사용 => 적응됐다고 판단하면 hooks 등 다른거 쓰자
- 함수형 써야할 때 => 라이프사이클이 필요 없이 바로 리턴할 때 (단순한 컴포넌트)



