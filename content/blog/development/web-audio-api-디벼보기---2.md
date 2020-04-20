---
title: Web Audio API 디벼보기 - 2 (사실은 Redux)
date: 2020-04-20 17:04:32
category: development
draft: false
---

[저번 포스팅]("https://saengmotmi.netlify.app/development/web-audio-api-%EB%94%94%EB%B2%BC%EB%B3%B4%EA%B8%B0---1/")에서는 Web Audio API의 기본적인 세팅 및 재생, 정지, 일시정지, 볼륨 조절 등의 기능을 구현하는 데 집중했다. 이번 포스팅에서는 제목과는 어울리지 않게 Web Audio API의 기능을 추가적으로 건드리지는 않고 Redux를 붙이는 작업을 해볼 것이다.

왜냐하면 이 모든 일들이 사운드클라우드 클로닝을 위한 연습이기 때문이다. 사운드클라우드에서는 각 플레이어마다, 그리고 하단 재생바에서 동일한 기능이 작동해야 한다. 그러자면 전역 상태관리가 필수적이다.

저번 포스팅의 결과물에 Redux를 적용하기 위해 약간의 리팩토링을 했다.

컴포넌트는 App, Buttons, VolumeControler로 총 3개다. 그리고 버튼과 inputBox에 들어가는 함수들은 모두 별도 파일로 분리했다. Redux에 필요한 파일들은 src/store에 들어 있고 actions와 reducers 폴더를 만들고 그 안에 각각 index.js를 만들었다. index.js로 만드는 이유는 [이 포스팅]("https://saengmotmi.netlify.app/development/2020-04-10-%EA%B8%B0%EC%97%85%ED%98%91%EC%97%85-3%EC%A3%BC%EC%B0%A8-%EA%B8%88%EC%9A%94%EC%9D%BC/")을 참고할 것.

가장 먼저 루트 폴더에 위치한 `index.js`다. Provider 컴포넌트로 전체 앱을 감싸고 `createStore()`로 생성한 전역 store를 Provider에 붙여줬다. 다시 store에는 rootReducer를 붙여줬다. 이에 대한 소개는 뒤에서 하겠다.

```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import rootReducer from './store/reducers'

const store = createStore(rootReducer)

ReactDOM.render(
  <Provider store={store}>
    <React.StrictMode>
      <App />
    </React.StrictMode>
  </Provider>,
  document.getElementById('root')
)
```

그 다음 `App.js`다. 모양은 동일한데 코드 상으로는 많은 부분이 바뀌었다.

가장 큰 부분으로는 함수들을 전부 src/global/func.js로 분리했다. 또 useState로 관리하던 변수들을 전부 Redux로 대체했다.

이 컴포넌트에서 props로 받고 있는 건 전부 액션생성함수 뿐이다. 나는 이 함수들을 `setFunc` 객체에 묶어 하위 컴포넌트인 `Buttons`에 내려준 다음 그곳에서 해당 함수를 사용하도록 했다. `App` 컴포넌트에서 사용하고 있는 `getMusic()`처럼 말이다.

왜 굳이 이렇게 했는지를 묻는다면 한 컴포넌트에서 store의 변수와 액션생성함수를 동시에 받아오는 방법을 알지 못하기 때문이다, 라고 대답하겠다...(이렇게 하는게 맞는지 모르겠음)

```jsx
import React from 'react'
import { connect } from 'react-redux'
import Buttons from './Components/buttons'
import VolumeControler from './Components/VolumeControler'
import { regSrc, regCtx, regNode, setPause, setVolume } from './store/actions'
import { getMusic } from './global/func'

const App = ({ regSrc, regCtx, regNode, setPause, setVolume }) => {
  const setFunc = {
    regSrc: regSrc,
    regCtx: regCtx,
    regNode: regNode,
    setPause: setPause,
  }

  return (
    <div id="App">
      <button type="button" onClick={() => getMusic(regSrc, regCtx, regNode)}>
        다운로드
      </button>
      <Buttons {...setFunc} />
      <VolumeControler setVolume={setVolume} />
    </div>
  )
}

export default connect(null, { regSrc, regCtx, regNode, setPause, setVolume })(
  App
)
```

그 다음은 `Buttons` 컴포넌트다. `App.js`에서 내려 받은 액션생성함수와 전역 store의 state 값들이 모두 props로 들어오고 있다. 전역 state를 사용하기 위해서는 `mapStateToProps()`라는 함수를 만들어 해당 컴포넌트에 `connect()`로 연결시켜준 뒤 다시 props로 받아 사용한다.

```jsx
import React from 'react'
import { connect } from 'react-redux'
import { playMusic, stopMusic, pauseMusic } from '../global/func'

const Buttons = ({ src, ctx, isPause, regSrc, regCtx, regNode, setPause }) => {
  return (
    <>
      <button type="button" onClick={() => playMusic(src)}>
        재생
      </button>
      <button
        type="button"
        onClick={() => stopMusic(src, regSrc, regCtx, setPause)}
      >
        중지
      </button>
      <button type="button" onClick={() => pauseMusic(ctx, isPause, setPause)}>
        일시정지
      </button>
    </>
  )
}

const mapStateToProps = state => {
  return {
    src: state.rootSrc,
    ctx: state.rootCtx,
    isPause: state.isPause,
  }
}

export default connect(mapStateToProps)(Buttons)
```

다음은 `func.js`로 실제 기능을 동작시키는 함수들이다. 지난 포스팅과 다른 점은 별도 파일로 분리되었다는 점과 파라미터로 액션생성함수 및 전역변수를 받고 있다는 점 뿐이다.

```jsx
export const getMusic = async (regSrc, regCtx, regNode) => {
  const audioCtx = new (window.AudioContext || window.webkitAudioContext)()

  const musicData = await fetch('http://localhost:3000/song2.mp3')
  const musicDataBuffer = await musicData.arrayBuffer()
  const decodedData = await audioCtx.decodeAudioData(musicDataBuffer)
  const source = audioCtx.createBufferSource()
  const gainNode = audioCtx.createGain()

  source.buffer = decodedData
  source.connect(gainNode)
  gainNode.connect(audioCtx.destination) // 출력을 연결, 기본은 스피커

  regSrc(source)
  regCtx(audioCtx)
  regNode(gainNode)
  console.log('ready')
}

export const playMusic = src => {
  const duration = src.buffer.duration
  src.start(0, 0, duration) // setTimeout, startPoint, playTime
  console.log('play')
}

export const stopMusic = (source, regSrc, regCtx, setPause) => {
  source.stop()
  regSrc(null)
  regCtx(null)
  setPause(false)
  console.log('stop')
}

export const pauseMusic = (ctx, isPause, setPause) => {
  isPause ? ctx.resume() : ctx.suspend()
  setPause(!isPause)
  console.log('pause', isPause)
}
```

그 다음으로는 Redux에 필요한 액션생성함수를 담은 `actions` 폴더의 `index.js`다. 사실 이것도 그냥 이렇게 하는게 맞는 건지 모르겠다...

```jsx
export const regSrc = src => {
  return {
    type: 'REGISTER_SOURCE',
    payload: src,
  }
}

export const regCtx = ctx => {
  return {
    type: 'REGISTER_CONTEXT',
    payload: ctx,
  }
}

export const regNode = node => {
  return {
    type: 'REGISTER_NODE',
    payload: node,
  }
}

export const setPause = state => {
  return {
    type: 'SET_PAUSE',
    payload: state,
  }
}

export const setVolume = state => {
  return {
    type: 'SET_VOLUME',
    payload: state,
  }
}
```

이 액션생성함수의 실행 결과는 다시 `reducer` 폴더의 `index.js`로 이어진다. 리듀서들을 `combineReducers()`로 묶어 rootReducer를 만들어 준다. 위에서 언급했던 루트 폴더의 index.js 안의 rootReducer가 바로 이 친구다.

```jsx
import { combineReducers } from 'redux'

const rootSrc = (state = null, action) => {
  switch (action.type) {
    case 'REGISTER_SOURCE':
      return action.payload
    default:
      return state
  }
}

const rootCtx = (state = null, action) => {
  switch (action.type) {
    case 'REGISTER_CONTEXT':
      return action.payload
    default:
      return state
  }
}

const rootNode = (state = null, action) => {
  switch (action.type) {
    case 'REGISTER_NODE':
      return action.payload
    default:
      return state
  }
}

const isPause = (state = false, action) => {
  switch (action.type) {
    case 'SET_PAUSE':
      return action.payload
    default:
      return state
  }
}

const volume = (state = 0, action) => {
  switch (action.type) {
    case 'SET_VOLUME':
      return action.payload
    default:
      return state
  }
}

export default combineReducers({ rootSrc, rootCtx, rootNode, isPause, volume })
```

데이터의 흐름을 정리하자면 이렇다.

1. App 컴포넌트의 다운로드 버튼 클릭 => `getMusic()` 실행
2. store/actions의 `regSrc`, `regCtx`, `regNode` 액션생성함수 실행 => 루트 리듀서로 값 전달
3. 각각의 리듀서 실행 => store의 값 변경
4. `mapStateToProps()`를 컴포넌트에 연결, 전역 state를 props로 다시 받아 사용
5. func.js 안의 함수에 액션생성함수와 전역 state 전달

아직 리덕스의 구체적인 메커니즘, 예를 들면 `connnect()`, `combineReducers()` 등을 전부 이해하지는 못한 상태라 자세한 설명까지는 어려운 상태다. 다만 일단 이런 방식으로 기존에 구현했던 기능에 Redux를 적용할 수 있다는 점을 보인 데 의의를 두려 한다.
