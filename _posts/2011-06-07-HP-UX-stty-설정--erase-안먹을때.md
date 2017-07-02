---
title: HP-UX stty 설정 , erase 안먹을때
category: Linux
keywords: 
tags: 
published: 2011-06-07T19:18:09+09:00
modified: 2012-03-07T16:24:47+09:00
---
간혹 쉘에서 @를 입력하면 입력하던 라인의 내용이 지워져서 @를 입력하지 못하는 경우가 있다.&nbsp;  
  
TeleType 시스템에서 한 문자를 삭제하는 데는 #, 줄을 삭제하는 데는 @ 등의 인쇄 문자를 사용하여 입력 데이터를 편집했었는데, HP-UX는 아직까지도 #, @ 및 DEL을&nbsp; 기본 로그인 제어 문자로 사용하고 있다.  
  
설정 내용은 아래와 같이 입력하면 확인해 볼 수 있다.  

#&nbsp; stty -a  
min = 4; time = 0;  
intr = DEL; quit = ^\; erase = #; kill = @  
eof = ^D; eol = ^@; eol2 \<undef\>; swtch \<undef\>  
stop = ^S; start = ^Q; susp \<undef\>; dsusp \<undef\>  
werase \<undef\>; lnext \<undef\>  

  
변경하려면 다음과 같이 입력하자.  

# stty kill ^U  

^U는 [**Ctrl** - **U**]키를 누른다.  
  
매번 로그인 할때마다 설정하면 불편하므로 .profile 및 .login 스크립트에 stty 명령을 포함하여 DEL, # 및 @을 각각 ETX(Ctrl-C), 백스페이스(Ctrl-H) 및 NAK(Ctrl-U)와 같은 일반적으로 사용되는 제어 문자로 변경하자.  

stty intr&nbsp; ^C  
stty erase ^H  
stty kill ^U  

  
더 자세한 내용은 아래 링크나 첨부 파일의 "로그인 특수 문자 변경" 부분을 참고한다.  
