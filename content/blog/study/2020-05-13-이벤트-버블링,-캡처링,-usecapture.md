---
title: 2020-05-13 이벤트 버블링, 캡처링, useCapture Option
date: 2020-05-13 01:05:69
category: study
draft: false
---

이벤트 버블링, 캡처링이 대강 어떤 건지만 알고 있다가 오늘 조금 확실하게 이해하게 된 듯 하여 바로 정리! (준석님 감사합니다!)

## 이벤트 캡처링, 버블링

다음은 이벤트 객체가 DOM 트리에서 어떤 식으로 전파되는지를 나타낸 그림이다.

<div align="center"><img src="./images/051304.jpg" />이벤트 버블링 / 캡처링 (출처 : w3.org)</div>

<div align="center"><img src="./images/051301.jpg" /></div>

<div align="center"><img src="./images/051302.jpg" /></div>

<div align="center"><img src="./images/051303.jpg" /></div>

## 이벤트 델리게이션 (delegation, 위임)

이벤트 델리게이션은 별도의 개념 같지만 그 핵심에는 이벤트 버블링이 들어 앉아있다. 이를테면 실용적인 패턴 쯤으로 보아도 무방하지 않겠냐는 거다.

참조 :

- [EventTarget.addEventListener()](https://developer.mozilla.org/ko/docs/Web/API/EventTarget/addEventListener)
