---
title: 2020-02-05 인스타그램 클론 코딩 완료
date: 2020-02-05 23:02:01
category: development
draft: false
---

짧은 시간이었지만 그간 배운 HTML, CSS, JS를 활용해 인스타그램의 메인페이지와 로그인 페이지를 클론 코딩했다.

아래 코드는 메인 페이지에 자잘하게 들어가는 기능들을 구현하는데 사용된 자바스크립트 내용이다.

주로 DOM의 동적 생성과 컨트롤, 이벤트 리스너와 이벤트 객체를 활용한 인터렉션 등의 내용이다.

```javascript
const articleComment = document.getElementsByClassName('comment-list')[0]
const commentForm = document.getElementsByClassName('comment-form')[0]
const commentText = document.getElementsByClassName('comment-text')[0]
const commentBtn = document.getElementsByClassName('comment-button')[0]

const searchInput = document.getElementsByClassName('nav-search')[0]
const overInput = document.getElementsByClassName('nav-over-search')[0]

function addComment(e) {
  console.log(e.keyCode)
  console.log(e.keyCode !== e.shiftKey)
  console.log(e.shiftKey)

  if (e.keyCode === 13) {
    e.preventDefault()
  }

  if (
    (commentText.value !== '' && e.keyCode === 13 && !e.shiftKey) ||
    (commentText.value !== '' && e.type === 'click')
  ) {
    const CommentWrap = document.createElement('div')

    const newComment = document.createElement('p')
    const newCommentText = document.createTextNode(' ' + commentText.value)
    const urlProfile = 'https://instagram.com/falsekind_'

    //삭제 버튼 정의
    const newCommentDelete = document.createElement('div')
    const newCommentDeleteX = document.createTextNode('✖️')

    //댓글 내용 추가
    newComment.innerHTML = '<a href=' + urlProfile + '><b>falsekind_</b></a>'
    newComment.appendChild(newCommentText)

    //삭제 버튼 추가
    newCommentDelete.appendChild(newCommentDeleteX)

    CommentWrap.appendChild(newComment)
    CommentWrap.appendChild(newCommentDelete)
    articleComment.appendChild(CommentWrap)

    CommentWrap.className = 'comment-element'
    newCommentDelete.className = 'delete-comment'

    commentText.value = ''

    //댓글 삭제
    const deleteComment = document.getElementsByClassName('delete-comment')
    const commentIndex = deleteComment.length - 1

    for (let i = 0; i <= commentIndex; i++) {
      deleteComment[i].addEventListener('click', function() {
        this.parentNode.remove()
      })
    }

    //deleteComment[commentIndex].addEventListener('click', DeleteDamnComment(1, 2)); //얘는 왜 될까? <- 인수 받겠다고 괄호 넣어서 안됐던거임... sjfkjdsfkljasd
  }
}

function moveFocus() {
  overInput.classList.add('invisible')
  searchInput.focus()
}

function lostFocus() {
  overInput.classList.remove('invisible')
  if (searchInput.value === '') {
    overInput.innerHTML = '<p>검색</p>'
  } else {
    overInput.innerHTML = '<p>' + searchInput.value + '</p>'
  }
}

//댓글 달기
commentText.addEventListener('keypress', addComment)
commentBtn.addEventListener('click', addComment)

//상단 내비게이터 검색창
overInput.addEventListener('click', moveFocus)
searchInput.addEventListener('blur', lostFocus)
```
