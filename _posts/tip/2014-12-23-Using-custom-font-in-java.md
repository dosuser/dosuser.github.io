---
title: "Using custom font in java"
category: memo
keywords: tip, java
tags: tip, java
published: 2014-12-23T15:01:27+09:00
modified: 2014-12-23T15:01:38+09:00
---
자바에서 폰트를 사용할때 보통 폰트를 로컬에 설치해서 사용하는 방법 이외에 동적으로 로드해서 사용하는 방법이 있다.

Font.createFont 를 사용하면 font 파일을 읽어서 폰트 객체로 넘겨준다.

또한 이를 이용하면 테스트 케이스도 돌릴 수 있어서 좀 더 깨끗한 코드가 나오는것 같다.

