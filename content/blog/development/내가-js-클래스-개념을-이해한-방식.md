---
title: 2020-02-08 내가 JavaScript 클래스 개념을 이해한 방식
date: 2020-02-08 14:02:69
category: development
draft: false
---

enemy rains 게임(플레이어가 이동하며 하늘에서 떨어지는 귀신 이미지를 터치하는 방식)의 핵심 기능들에 대한 구현이 끝났다.

setInterval() 함수의 다소 생소한 작동방식이 문제였지 그 외적인 것들이 썩 어렵지는 않은 편이었다.

## 되긴 되는데... 문제가 있습니다

그런데 게임을 완성했다고 하기에는 해결 하지 못한 문제가 남아 있었다.

1. 목적이 다른 setInterval 함수 간 타이밍 문제
2. 인터벌마다 컨트롤하고자 하는 element 들을 배열에 매번 재할당 해야 하는 비효율

1번의 경우 새 고스트 element를 생성하고 이동시키는 setInterval, 일정 시간이 지난 다음 차례로 고스트 element를 제거하는 setInterval 두 개가 따로 돌았다. 이 경우 화면에 그려지는 개체가 많아지거나 두 인터벌의 타이밍이 꼬일 경우 고스트들이 제대로 제거되지 않거나 고스트 생성 위치가 틀어지는 문제가 발생했다.

2번의 경우 성능 저하 문제가 있었다. 내가 구현한 방식은 이렇다.

- setInterval을 사용해 일정 시간 마다 ghost-down이라는 클래스명을 가진 새 고스트를 생성한다
- 일정 인터벌마다 화면에 getElementsbyClassName을 사용해 화면에 그려진 ghost-down 클래스 element들을 배열에 담아온다
- 반복문을 활용해 그 배열의 인덱스를 0부터 arr.length까지 돌면서 특정 px씩 아래로 이동시킨다
- 특정 높이에 도달했을 경우 피격 판정을 준다

그러나 이 경우 배열에 담아야 하는 element의 수가 많아질 수록 연산량이 급증했고, 1번과 유사한 오류를 발생시켰다.

## Class를 활용해 고스트를 찍어내자

이러한 문제를 해결하려면 자바스크립트의 class 개념을 활용해야 한다. 개념이 조금 추상적이라 이해하는데 시간이 걸렸다. OOP(Object Oriented Programming)에 대해서도 제대로 아는 상태가 아니라 추가적인 포스팅을 통해 다뤄보려 한다.

일단 여기서는 class를 이해한 방식에 집중하자. 나는 class라는 개념을 다음과 같이 이해했다.

> - class는 object를 생성하는 일종의 프로그래밍적 틀이다. 즉, '내부는 비어있으면서 특징(구조)만 가지고 있는 뭔가'로 부를 수 있다. 붕어빵 틀과 붕어빵을 떠올리면 쉽다.
>
> - const class = new Enemy(); 등으로 object를 생성할 수 있다 (setInterval 내에 위치할 경우 인터벌마다 Enemy 객체가 선언된다. 이 과정을 인스턴스화라고 부른다.)
> - class로 생성된 object들은 일단 한번 생성되고 나면 각기 생명력을 가진 하나의 물체가 된다.
> - class는 constructor()로 시작하며 뒤이어 지는 메소드, 프로퍼티 등으로 구성된다. 이는 별도의 파일에서 함수를 실행하고 정의하는 등의 행위를 하는 것에 빗대어볼 수 있다. (예컨대 constructor에서 실행되는 코드들은 프로그램이 시작하고 나서 곧바로 실행되는 코드들에 대응하고, 메소드는 function(){} 등으로 정의되는 함수, 프로퍼티는 const/let으로 정의되는 변수에 대응한다.)

```javascript
const bg = document.getElementById('bg')
const ghostStep = 3 //픽셀
const ghostStepInterval = 50 //밀리세컨드
const ghostGenerateInterval = 2000 //밀리세컨드
let killScore = 0

setInterval(function() {
  const ghost = document.createElement('div')
  let randX = Math.random() * 755

  ghost.className = 'ghost-down'
  ghost.setAttribute('style', 'left: ' + parseInt(randX) + 'px; top: 0px;')
  //ghost.setAttribute('style', 'top: 0px;'); //그냥 ghost.style.top 안되나?
  bg.appendChild(ghost)
}, ghostGenerateInterval)

setInterval(function() {
  let ghostMoveDown = document.getElementsByClassName('ghost-down') //배열 인덱스를 사용하지 않으면 속도 개선 + 세밀한 조작 가능
  for (let i = 0; i < ghostMoveDown.length; i++) {
    //예컨대 객체를 사용한다든가
    ghostMoveDown[i].style.top =
      parseInt(ghostMoveDown[i].style.top) + ghostStep + 'px'

    //피격 판정
    //매 인터벌 마다 높이를 체크하면?
    if (parseInt(ghostMoveDown[i].style.top) > 490) {
      let heroLeft = parseInt(hero.style.left)
      let heroRight = parseInt(hero.style.left) + 35
      let ghostLeft = parseInt(ghostMoveDown[i].style.left)
      let ghostRight = parseInt(ghostMoveDown[i].style.left) + 45

      if (heroLeft <= ghostRight && ghostRight <= heroRight) {
        ghostMoveDown[i].id = 'ghost-dead'
      } else if (heroRight >= ghostLeft && heroRight <= ghostRight) {
        ghostMoveDown[i].id = 'ghost-dead'
      } else if (parseInt(ghostMoveDown[i].style.top) > 545) {
        ghostMoveDown[i].id = 'ghost-dead'
      }
    }
  }
}, ghostStepInterval)

setTimeout(function() {
  setInterval(() => {
    let ghostDisappear = document.getElementById('ghost-dead')
    ghostDisappear.remove()
  }, ghostGenerateInterval)
}, 545 / (ghostStep / ghostStepInterval))
```

그리고 그 다음은 class 생성자를 사용해 새롭게 작성한 코드다.

```javascript
const bg = document.getElementById('bg') //없어도 작동함 -> html에 부여된 id가 전역변수처럼(?) 작동해서
const ghostStep = 3 //픽셀
const ghostStepInterval = 20 //밀리세컨드
const ghostGenerateInterval = 1000 //밀리세컨드
let killScore = 0

class Enemy {
  constructor() {
    this.ghost = document.createElement('div')
    this.createGhost()
    this.moveDown()
    this.isDead()
  }

  createGhost = () => {
    //createElement를 사용해 고스트를 생성
    let randX = Math.random() * 755

    this.ghost.className = 'ghost-down'

    this.ghost.style.left = parseInt(randX) + 'px'
    this.ghost.style.top = '0'
    bg.appendChild(this.ghost)
  }

  moveDown = () => {
    setInterval(() => {
      this.ghost.style.top = parseInt(this.ghost.style.top) + ghostStep + 'px'
      this.isDead()
    }, ghostStepInterval)
  }

  isDead = () => {
    //createGhost에서 생성된 고스트를 setInterval을 사용해 아래로 이동
    //조건을 판정해 만약 죽었으면 isDead() 호출

    if (parseInt(this.ghost.style.top) > 490) {
      let heroLeft = parseInt(myHero.heroId.style.left)
      let heroRight = parseInt(myHero.heroId.style.left) + 35
      let ghostLeft = parseInt(this.ghost.style.left)
      let ghostRight = parseInt(this.ghost.style.left) + 45

      if (heroLeft <= ghostRight && ghostRight <= heroRight) {
        this.ghost.classList.add('ghost-dead')
      } else if (heroRight >= ghostLeft && heroRight <= ghostRight) {
        this.ghost.classList.add('ghost-dead')
      }

      if (parseInt(this.ghost.style.top) > 545) {
        this.ghost.remove()
      }
    }
  }
}

setInterval(() => {
  const a = new Enemy()
}, ghostGenerateInterval) // 인터벌 마다 Enemy 객체를 생성함
```
