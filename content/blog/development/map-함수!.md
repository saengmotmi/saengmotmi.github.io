---
title: map 함수!
date: 2020-02-16 21:02:50
category: development
draft: false
---

### 반복은 귀찮아

HTML/CSS/JS/리액트로 인스타그램 클론 코딩을 하면서 각 부분에 대한 레이아웃을 똑같이 작성해본 건 꽤나 흥미로운 경험이었다. 실제 제공되고 있는 서비스의 면면을 뜯어보기도 하고, 내가 만든걸 여기 저기 자랑도 하고...

그러나 걷다 보면 앉고 싶고, 앉으면 눕고 싶듯이(?) 모양을 똑같이 만들고 보니 실제 서비스와 다른 부분을 조금씩 채워넣고 싶어졌다. 그것들 중 하나가 **끝없이 쏟아지는 피드**였다. 리액트도 배웠겠다 그걸 좀 쌈뽕나게 해보자는건데...

JSON 등 데이터 통신 쪽에는 아직 지식이 없어서 손으로 직접 데이터를 넣어두고 컴포넌트를 여러 개 반복해 뿌려주기로 했다.

이는 map 함수를 사용하면 구현가능하다.

### map();

map 함수는 배열(1)을 받아와 각 요소를 프로세싱해 새로운 배열인 배열(2)를 생성한다.

함수는 `arr.map(callback, [thisArg : callback 실행 시 this로 사용되는 값])`의 구조로 사용한다. 다시 callback 자리에는

1. currentValue(현재 처리되는 요소)
2. index(cV의 인덱스)
3. array(메소드가 불려진 배열)

세 가지 파라미터가 들어간다. 반환되는 값은 배열의 각 요소에 대해 실행한 callback의 결과를 모은 새로운 배열이다. 더 자세히 보려면 [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map)을 참조하자.

잊지 말아야 할 것은 각 요소들에 key 값을 부여하지 않으면 오류가 난다. 잊지 말고 인덱스를 넣어주자.

그럼 내가 하고 싶은 건 포스트의 반복 생성이므로, 다음과 같은 절차를 거치면 된다.

1. 생성하고자 하는 각각의 포스트에 들어갈 데이터(state)들을 객체에 담아주고,
2. 그 객체들을 배열로 만들어서,
3. map 메소드를 사용해 자식 컴포넌트에 데이터를 넣어준다.

아래는 피드 섹션 전체의 코드다. 피드 안쪽에 포스트 여러 개가 들어가는 구조다.

```jsx
class Feed extends Component {
  constructor(props) {
    super(props)
    this.state = {
      postData: [
        // 데이터 준비
        {
          myId: 1,
          myUsrId: 'falsekind_',
          myImgSrc: '/images/my-profile-img.jpg',
          mySrc: 'http://instagram.com/falsekind_',
          myPostImgSrc: '/images/my-image.jpg',
          postBody: '갑자기 분위기 미국 (가로버전) pic by @minje.55',
        },
        {
          myId: 2,
          myUsrId: 'falsekind_',
          myImgSrc: '/images/my-profile-img.jpg',
          mySrc: 'http://instagram.com/falsekind_',
          myPostImgSrc: '/images/1.jpg',
          postBody: '올해 첫눈',
        },
      ],
    }
  }

  render() {
    // map 함수를 실행하면 postDataList에 새로운 배열이 결과물로 담긴다
    const postDataList = this.state.postData.map(param => {
      // 준비한 데이터는 postData에 담겨 있음
      return (
        <FeedPost // FeedPost라는 자식 컴포넌트에 props로 데이터들을 찢어서 전달
          key={param.myId}
          myUsrId={param.myUsrId}
          myImgSrc={param.myImgSrc}
          mySrc={param.mySrc}
          myPostImgSrc={param.myPostImgSrc}
          postBody={param.postBody}
        />
      )
    }) // 새로운 배열 안에는 <article> 태그로 각각 묶인 2개의 포스트가 들어 있다

    return (
      <div className="body">
        <Nav />
        <main>
          <div className="feeds">{postDataList}</div>
          <Aside userData={this.state.userData} />
        </main>
      </div>
    ) // feeds라는 클래스 네임을 가진 div 태그 아래에 PostDataList의 내용이 출력!
  }
}

export default Feed
```

아래는 개별 포스트를 그려주는 자식 컴포넌트 코드의 일부다.

```jsx
class FeedPost extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        const { key, myUsrId, myImgSrc, mySrc, myPostImgSrc, postBody } = this.props;
        // props로 받아온 값들을 비구조화 할당으로 찢어 좀 더 간단하게 컴포넌트 내에 뿌려줄 수 있다.
        return(
            <article id={key}>
                <div className="post-account-name">
                    <img alt={{myUsrId} + "님의 프로필 사진"} className="_6q-tv" src={myImgSrc} />
                    <div><a href={mySrc}>{myUsrId}</a></div>
                </div>
            (...)
```
