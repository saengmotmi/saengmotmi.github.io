---
title: 2020-04-22 git stash & soft, redux
date: 2020-04-22 18:04:25
category: development
draft: false
---

### 1. git reset, stach, force push로 실수 만회하기

오늘의 문제 상황을 간단히 설명하자면 이렇다.

1. 두달 전 작성했던 프로젝트 수정 중 master branch에서 작업하고 있었다는 사실을 뒤늦게 깨달음
2. 일주일 전 다른 팀원이 repo를 수정했다는 사실을 깨달음
3. 이미 내 수정사항은 commit까지 된 상태

그래서 나는 내 로컬의 상태를 잠시 마지막 fix 상태로 되돌린 다음 remote master의 수정사항을 내 local master에 반영해야 했다.

`git reset --soft`로 commit들을 잠시 staged 상태로 되돌린다. 그 수정 내용들은 `git stash save`해서 임시로 별도 저장한 뒤 local master에 `pull`을 받는다. 다시 `git stash apply`로 수정 내용을 복원한 뒤 `commit`한다.

명령어 라인으로 정리하면 다음과 같다.

```bash
git reset --soft {commit-hash}

git status ## staged 상태로 변한 파일들이 내가 의도한 것들인지 확인
git stash save "이름"
git stash list ## stash 인덱스 확인
git pull origin master
git stash apply stash@\{0\}

git add .
git commit -m "commit message"
git push origin master
```

### 2. package-lock.json과 node_modules

그런데 `stash apply`를 하는 순간 충돌이 발생했었다. 다른 파일이 아니라 package-lock.json에서 발생했는데 한두군데 충돌 난게 아니라 순간 이건 뭐지 싶었다. 예전에 package-lock.json에 대해서는 스쳐지나가듯 들은게 있어서 바로 검색을 했다.

[검색했더니](https://medium.com/@han7096/package-lock-json-%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC-5652f90b734c) 다음과 같은 문장이 나온다.

> package-lock.json은 npm을 사용하여 package.json 파일을 또는 node_modules 트리를 수정하면 자동으로 생성되는 파일이다. 한마디로 파일이 생성되는 시점의 의존성 트리에 대한 정보를 가지고 있다.

node_modules를 git repo에 업로드 하지 않기 때문에 대신 이 파일의 의존성 트리 기록을 기준으로 npm install 관리를 하는 듯 하다. 에러 메시지를 보니 node_modules 폴더와 package-lock.json(절대 package.json이 아니다!)을 삭제하고 문제가 된 의존성을 package.json에서 지운 뒤 `npm install`을 하라고 나온다. 그러면 다시 node_modules 폴더와 package-lock.json 파일이 재구성된다.

### 3. git push --force

그러나 아차. 2번 문제를 해결하기 전에 PR을 처리해버렸다. master를 바꿔야 할 상황이 됐다. 로컬에서 제대로 작동하는지 확인하지 않고 덜렁댄 탓이다. 다시 2번 문제를 해결하고 돌아와 제대로 작동하는지 여부, remote master의 최신화 상태를 보고 force push를 했다.

```bash
git push origin master --force
```

### 4. `Connect()`와 Reducer (Redux)

이번엔 사클 리팩토링을 위해 연습 중인 리덕스 프로젝트다.

Provider에 작성한 store에 접근하기 위해서는 `connect()` 함수로 redux store를 사용하고자 하는 컴포넌트를 감싸준 뒤 필요한 요소를 해당 컴포넌트의 props로 받아 써야 한다. 다음과 같이 말이다. 액션생성 함수도 깨알같이 spread 연산자를 이용해 정리해줬다.

```jsx
import React, { useEffect } from 'react'
import { connect } from 'react-redux'
import { setVolume } from '../store/actions'

const setFunc = {
  setVolume: setVolume,
}

const App = ({ setVolume }) => {
  return (
    <div id="App">
      <Buttons />
      <VolumeControler setVolume={setVolume} />
    </div>
  )
}

export default connect(null, { ...setFunc })(App) //highlight-line
```

위 코드블럭에서 하이라이트 한 부분이 해당 컴포넌트가 redux store와 연결되는 부분이다. connect 함수는 두 개의 인자를 받는다. 첫번째 인자에는 전역 store의 state, 두번째 인자에는 액션생성 함수가 연결된다. 그런데 프레젠테이셔널 컴포넌트니 뭐니 그런 말은 말만 복잡하지 실상 이해에 도움이 되는 부분이 없다. 그냥 connect 함수 파라미터의 첫번째는 state, 두번째는 set함수라 외우자.

```js
connect(mapStateToProps, ActionFunction)(컴포넌트)
```

그리고 동일 음원에 대한 데이터를 담던 reducer 세 개를 하나로 줄였다. [이전 포스팅](https://saengmotmi.netlify.app/development/web-audio-api-%EB%94%94%EB%B2%BC%EB%B3%B4%EA%B8%B0---2/)과 비교해보면 좋겠다.

```jsx
const music = (state = null, action) => {
  switch (action.type) {
    case 'REGISTER_SOURCE':
      return { ...state, src: action.payload }
    case 'REGISTER_CONTEXT':
      return { ...state, ctx: action.payload }
    case 'REGISTER_NODE':
      return { ...state, node: action.payload }
    default:
      return state
  }
}
```

mapStateToProps로 받아주는 부분에서도 하나의 객체로 받아주자. 그러면 이제 사용할 때는 music.src, music.ctx 등으로 사용하면 된다. 훨씬 명료하고 관리하기 편해졌다.

```jsx
const mapStateToProps = state => {
  return {
    music: state.music,
    isPause: state.isPause,
  }
}
```

reducer는 어렵게 생각할 거 없이 그냥 전역 state의 키 값이라고 생각하면 편하다. mapToStateToProps 함수에서 받아와 쓰는 모습만 봐도 그렇고... 암튼 이렇게 쓰면 된다. 이젠 정말정말 적용 준비 끝!
