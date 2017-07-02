---
layout: post
title: "lombok을 쓰기 시작하면서...."
category: blog
keywords: [java]
tags: 
published: 2014-01-01T17:25:25+09:00
modified: 2014-01-01T17:25:46+09:00
---
lombok ([http://projectlombok.org/](http://projectlombok.org/))&nbsp;을 쓰기시작한지 1주일이 되었습니다.&nbsp;

아직 뭐라고 하기 어려운 상황에서 서버 프로그래머의 간단히 느낀점을 이야기 해볼까 합니다.

1. 왜 쓸까?

2013년 8월경에 lombok을 쓰자는 이야기가 나왔을 때 저의 반응은 "왜?" 였습니다.&nbsp;

서버 프로그래밍에서 많은 경우 모델객체는 한번 쓰고 수정되는 경우는 적습니다.&nbsp;

그리고 getter, setter, toString&nbsp;정도는 이클립스에서 생성해줍니다.

그 당시에는 그다지 쓸 필요를 느끼지 못했습니다.&nbsp;

2. 써보자!

서버 로직이 아닌 툴을&nbsp;만들면서 선임께서 lombok을 쓰자고 하셨습니다.&nbsp;

모델 객체 만드는 시간적 비용을 줄이고, 좀 더 스크립트 언어 처럼 개발하자! 라는 이유 입니다.&nbsp;

3. 써봤더니 이상하다

getter, setter, toString이런거 까지는 뭐 쓸만한데, deligate가 문제 였습니다.&nbsp;

저는 평상시에 모델객체가 composite &nbsp;되는 경우에 deligate를 사용하였는데

lombok은 A,B 객체간&nbsp;충돌난 메소드를 구별하기 위해서 interface를 사용하여 한쪽의 특정 메소드들을 제거 할 수 있습니다.&nbsp;

문제는 이렇게 될 경우 나중에 lombok을 이용하여 deligate를 사용할 경우 기존 클래스에 interface를 상속해 주어야 합니다.&nbsp;

-0-

4. lombok은 잘못이 없다.&nbsp;

이런점을 공유 했더니 돌아온 피드백은 "모델을 그렇게 쓰는게 맞을까?, 우선 lombok이 원하는건 그게 아닌데" 였습니다.&nbsp;

모델 객체를 만드는 것에 대해서는 좀더 &nbsp;공부를 해야 할 것 같습니다.&nbsp;

5. lombok을 쓰면 변경에 좀 더 자유롭다

getter,setter, toString을 직접 개발 하게 되면 eclipse를 쓰던 안쓰던, 맴버 변수를 추가, 삭제할 경우 귀찮아 집니다.&nbsp;

정말정말 귀찮아 지는데, 유지보수가 아니라 초기 개발의 경우 더욱 그렇게 되는것 같습니다.

lombok을 쓰면 알아서 메소드들을 만들어&nbsp;주니까 변경에 좀 더 자유롭습니다.

그리고 좀 더 일관적인 코드 개발이 가능해 집니다.&nbsp;

6. lombok을&nbsp;좀 더 써봐야겠습니다.&nbsp;

이제 1% 정도 알겠고 자세한건 잘 모르겠습니다.&nbsp;

더 써봐야 겠네요 ㅜㅜ

&nbsp;

