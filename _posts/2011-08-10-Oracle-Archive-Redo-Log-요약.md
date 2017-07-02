---
layout: post
title: Oracle Archive Redo Log 요약
category: 프로그래밍일반
keywords: archive, oracle, redo, redolog, 오라클
tags: archive oracle redo redolog 오라클
published: 2011-08-10T10:57:17+09:00
modified: 2011-10-10T09:45:33+09:00
---
**Archive Redo Log 요약**



**Redo 로그**

Oracle Log Writer는 <u>redo와 undo를 하기 위해 필요한 모든 데이터</u>를 저장합니다.

redo로그는 복수의 파일에 쓰여지며 해당 파일이 꽉 찼을 경우 다음 파일로switching됩니다.

switching당하는 경우 파일의 기존의 내용은 날라갑니다.

redo로그의 크기는 데이터 변경 분의 2배 이상이 됩니다.

&nbsp;

**Archive redo 로그**

Redo log의 유실을 막기 위해서는 redo log를 모두 저장 해야 합니다.

archiver process는 redo log가 꽉 찬 경우 이를archive log로 기록합니다.

Log가 archive되어야만LGWR가 동작 하기 시작하며 archive작업이 디스크 full등으로 정지 될 경우 LGWR이 동작하지 않아 DBMS가 hang됩니다.

기본적으로 오라클은 no archive mode입니다.

&nbsp;

![](/attachments/2011-08-10-figure1.png)


