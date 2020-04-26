---
title: 2020-03-07 AWS에 CRA 프로젝트 배포하기
date: 2020-03-07 18:03:07
category: development
draft: false
---

2주 간 마켓 컬리 사이트를 클론 코딩하는 1차 프로젝트가 끝이 났다. 아쉬운 점이야 태산 같지만 그래도 첫 프로젝트인데다 앞으로 해나가야 할 것들이 훨씬 많다. 지금 느끼는 부족함이야 천천히 채워나가면 되지 않을까 싶다.

부족함은 부족함이고 내 작업물을 주변에 보여주고 싶다면 배포(deploy)를 해야 한다. 나는 CRA(Create-React-App)을 통해 작업했기 때문에 기준은 CRA다.

우선 내 PC 환경을 대신할 가상 서버가 필요하다. AWS EC2(Amazon Elastic Compute Cloud)를 사용해 인스턴스를 만들자. 여기서 인스턴스는 "가상 컴퓨팅 환경"을 뜻한다. 절차는 [여기](https://windosakacastle.tistory.com/12)를 참고해서 그대로 따라하면 된다.

주의할 점은 위 과정 중 key pair를 하나 다운 받게 되는데 이 프라이빗 키를 통해 접속하게 된다. 분실하면 안된다.

EC2 인스턴스 설정 과정이 정상적으로 마무리 됐다면 이제 로컬에서 SSH를 통해 터미널에 원격 접속할 차례다.

```bash
ssh -i [키 파일 경로/키 이름.pem] ubuntu@publicIP
```

여기서 ubuntu는 해당 인스턴스의 username을 적으면 된다. 인스턴스를 생성하면 ubuntu는 기본적으로 존재하는 유저명이고 따라서 root라는 유저명도 사용 가능하다. 형민님이 인스턴스 환경을 윈도우즈로 만들어서 접속을 못했었어서 덧붙임.

그런데 SSH가 뭔지 몰라서 tldr(Too Long Don't Read)에 쳐봤다. 위 명령은 원격 서버에 개인 ID를 가지고 접속할 때 사용되는 것 같다.

> ssh
>
> Secure Shell is a protocol used to securely log onto remote systems.
> It can be used for logging or executing commands on a remote server.
>
> - Connect to a remote server with a specific identity (private key):
> - ssh -i {{path/to/key_file}} {{username}}@{{remote_host}}

접속에 성공했다면 이제 우분투를 설치할 때와 마찬가지로 초기 환경을 세팅해주면 된다. 일단 지금 필요한 건 npm과 node.js다. npm은 node.js를 설치하면 자동으로 딸려오니 별도 설치할 필요는 없다.

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo bash -
sudo apt-get update
sudo apt-get install nodejs
```

설치가 끝났으면 git에서 프로젝트를 clone 받은 뒤 필요한 설치를 해준다.

```
git clone REPOSITORYADDRESS
cd PATH
npm install express --save
```

또한 지금까지는 npm start 등으로 로컬 서버를 구동시켜 왔지만 배포시에는 해당 코드를 배포 버전으로 build 시켜야 한다. npm run [스크립트명]으로 실행한다.

```
npm run build
```

그리고 clone 받은 프로젝트 최상위폴더에 server.js라는 파일을 만들고 열어서

```
touch server.js
vi server.js
```

아래 파일을 붙여넣어준다.

```js
const express = require('express')
const path = require('path')
const app = express()

app.get('/ping', function(req, res) {
  res.send('pong')
})

app.use('/', express.static(path.join(__dirname, 'build')))

app.get('/*', function(req, res) {
  res.set({
    'Cache-Control': 'no-cache, no-store, must-revalidate',
    Pragma: 'no-cache',
    Date: new Date(),
  })

  res.sendFile(path.join(__dirname, 'build', 'index.html'))
})

app.listen(80, () => {
  console.log('success!')
})
```

이제 node 명령어를 통해 서버를 구동시켜주면 된다.

```
node server.js
```

그러나 위 방식은 포어그라운드에서 명령이 실행되기 때문에 터미널 접속을 끊으면 서버를 구동시키고 있는 프로세스 또한 함께 종료된다. 터미널을 끄면 외부에서 24시간 접속할 수 없게 된다는 뜻이다.

백그라운드 실행을 원할 경우 아래처럼 뒤에 `&`를 하나 붙여주면 백그라운드에서 실행되고 Process ID가 출력된다. 종료할 때는 해당 id로 종료(kill ProcessID)해주면 된다.

```
node server.js &
```
