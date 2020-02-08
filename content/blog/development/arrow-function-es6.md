---
title: arrow function ES6
date: 2020-02-08 14:02:66
category: development
draft: false
---

enemy rains 게임(플레이어가 이동하며 하늘에서 떨어지는 귀신 이미지를 터치하는 방식)의 핵심 기능들에 대한 구현이 끝났다. setInterval() 함수의 다소 생소한 작동방식이 문제였지 그 외적인 것들이 썩 어렵지는 않은 편이었다.

그런데 게임을 완성했다고 하기에는 해결 하지 못한 문제가 남아 있었다.

1. 목적이 다른 setInterval 함수 간 타이밍 문제
2. 인터벌마다 컨트롤하고자 하는 element 들을 배열에 매번 재할당 해야 하는 비효율

1번의 경우 새 고스트 element를 생성하고 이동시키는 setInterval, 일정 시간이 지난 다음 차례로 고스트 element를 제거하는 setInterval 두 개가 따로 돌았다. 이 경우 화면에 그려지는 개체가 많아지거나 두 인터벌의 타이밍이 꼬일 경우

class 개념을 이해하는 도중에 fucntion scope 문제로 this 객체를 사용하는데 문제가 발생할 수 있다는 조언을 들었다.




'''

'''