---
layout: post
title: "select2와 jquery ui modal 동시에 사용했을때"
category: memo
keywords: [java, tip]
tags: [java, tip]
published: 2014-09-28T17:42:12+09:00
modified: 2014-09-28T17:42:12+09:00
---

select2 와 jquery ui model을 동시에 사용하면서&nbsp;

모달폼 안에서 select2를 사용하면 포커스를 가지지 못하는 버그가 있다.&nbsp;

jquery-ui.js 안의 dialog 코드 안에 아래 예외 처리 코드를 넣으면 된다.

    return !!$(e.target).closest('.ui-dialog, .ui-datepicker, .select2-drop').length;

  

참고 URL

http://stackoverflow.com/questions/19787982/select2-plugin-and-jquery-ui-modal-dialogs

