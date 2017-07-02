---
layout: post
title: "Spring i18n, Locale 정리"
category: draft
keywords: [java]
tags: 
published: 2014-03-28T14:48:31+09:00
modified: 2014-03-28T14:48:31+09:00
---
&nbsp;

&nbsp;

Locale 설정을 위해서는

HandlerInterceptorAdapter, LocaleResovler 를 오버라이딩 해야 한다.


대체 message의 치환자&nbsp;는 {0}, {1} ....&nbsp; 형식으로 0 부터 시작하는 인덱스를 가진다.

또한 기본적으로 로케일이 UTF-8이 아니기 때문에 UTF-8로 작성한 리소스 번들을 제대로 사용하려면 리소스번들을 생성할떄 로케일을 지정해 주어야 한다.
