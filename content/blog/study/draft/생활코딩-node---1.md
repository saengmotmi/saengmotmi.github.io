---
title: 생활코딩 node - 1
date: 2020-05-18 14:05:22
category: study
draft: true
---

<div align="center"><iframe width="560" height="315" src="https://www.youtube.com/embed/Zhbvui_T9VY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

> http://localhost:8000/main?id=HTML&page=1

- http : protocol
  - 통신 규칙, 즉 사용자가 서버에 접속할 때 어떤 방식으로 통신할 것인가(http, ftp 등)
- localhost : host(domain)
  - 인터넷에 접속된 각 컴퓨터들을 host라고 부름. domain은 인터넷에 연결된 특정한 컴퓨터를 가리키는 주소.
- 3000 : port
  - 한 대의 컴퓨터 안에 여러 개의 서버가 있을 수 있음. 그 중 어떤 서버에 접속할지가 애매함. 80번 포트는 일반적으로 웹 통신을 위해 열려있음. 포트번호를 생략하면 이 포트로 접속함.
- main : path
  - 컴퓨터 안에 있는 어떤 디렉토리의 어떤 파일인지를 가리킴.
- id, page : query string
  - 쿼리스트링의 값을 변경하면 웹 서버에게 데이터를 전달할 수 있음. 시작은 ?, 값과 값의 연결은 &을, 값과 이름의 구분은 =로 하기로 약속되어 있음.
