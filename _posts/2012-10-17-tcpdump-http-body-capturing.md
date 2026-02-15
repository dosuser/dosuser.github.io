---
layout: post
title: tcpdump http body 본문 캡쳐 하기
date: 2012-10-17
author: SHIN DAE YONG
---
tcpdump를 이용하여 http 본문 메시지를 보고 싶을 때&nbsp;

tcpdump -A dst port 8080 -s 1500

-s 옵션을 주지 않으면 메시지가 &nbsp;잘려서 나오는 경우가 존재 한다.&nbsp;

아래와 같이 나오면 http 메시지가 잘려서 보일 것이다.

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode

listening on eth0, link-type EN10MB (Ethernet), **capture size 96 bytes**

이때 -s 옵션을 주어 capture 크기를 늘이면 된다.

  

