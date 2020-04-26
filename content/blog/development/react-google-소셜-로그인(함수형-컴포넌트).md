---
title: 2020-03-15 react google 소셜 로그인(함수형 컴포넌트)
date: 2020-03-15 23:03:43
category: development
draft: false
---

구글 소셜 로그인 기능을 담당했다. 고려해야 할 점은 1) react 2) 함수형 컴포넌트 라는 점이었다. 좋은 예제를 찾고자 했으나 내 능력이 부족한 탓인지 제대로 작동하는 코드를 찾기 힘들었다. 나중에 써먹을 목적으로 코드를 기록해둔다.

```jsx
// 함수형 컴포넌트 return 안에 들어갈 html, styled components로 작성했다
<Head>
  <script
    src="https://apis.google.com/js/platform.js?onload=init"
    async
    defer
  />
  <meta
    name="google-signin-client_id"
    content="클라이언트_ID.apps.googleusercontent.com"
  />
</Head>
```

내가 찾아본 예제들과 비교해 달라진 점은

- useRef, useEffect 훅 사용
- 클래스형 컴포넌트와 함수형 컴포넌트의 this 바인딩 및 scope 차이
- attachClickHandler 부분을 googleSDK로 가져와 합침
- Arrow Function으로 변경

auth.attachClickHandler() 부분에서 작동이 제대로 되지 않았던 점이 문제였다. 이는 useRef로 가져온 대상을 사용할 때 current 상에서 사용해야 하는데... 그 부분을 빼먹었기 때문이다.

```jsx
const googleLoginBtn = useRef(null)

const googleSDK = () => {
  // 구글 SDK 초기 설정
  window.googleSDKLoaded = () => {
    window.gapi.load('auth2', () => {
      const auth2 = window.gapi.auth2.getAuthInstance({
        client_id: '클라이언트_ID.apps.googleusercontent.com',
        scope: 'profile email',
      })
      auth2.attachClickHandler(
        googleLoginBtn.current, // useRef랑 current!!!!!
        {},
        googleUser => {
          const profile = googleUser.getBasicProfile()
          console.log(profile)
          console.log(`Token || ${googleUser.getAuthResponse().id_token}`)
          setToken(googleUser.getAuthResponse().id_token)
          console.log(`ID: ${profile.getId()}`)
          console.log(`Name: ${profile.getName()}`)
          console.log(`Image URL: ${profile.getImageUrl()}`)
          console.log(`Email: ${profile.getEmail()}`)
        },
        error => {
          alert(JSON.stringify(error, undefined, 2))
        }
      )
    })
  }
  // 구글 SDK 로드
  ;(function(d, s, id) {
    let js
    const fjs = d.getElementsByTagName(s)[0]
    if (d.getElementById(id)) {
      return
    }
    js = d.createElement(s)
    js.id = id
    js.src = 'https://apis.google.com/js/platform.js?onload=googleSDKLoaded'
    fjs.parentNode.insertBefore(js, fjs)
  })(document, 'script', 'google-jssdk')
}

useEffect(() => {
  googleSDK()
}, [])
```
