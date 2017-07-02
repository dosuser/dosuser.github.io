---
layout: post
title: "ReentrantLock 사용 예"
category: 대용이의 강의
keywords: code, java, lock, metv, reentrantlock, skb, transient
tags: code java lock metv reentrantlock skb transient
published: 2011-04-27T11:20:14+09:00
modified: 2011-04-27T11:20:14+09:00
---
  
참고  
http://download.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/locks/ReentrantLock.html  
  
RentrantLock의 사용상 주의 점  
  
fairness 설정  
fairness설정은 Lock이 걸려 대기 중인 쓰레드 중 가장 오래 기다린 쓰레드가 대기중인 다른 쓰레드 보다 먼저 처리 되도록 처리해 주는 것이다.  
faireness설정을 안한 이후에 Lock 이후 io작업을 했더니 기아 상태에 걸렸었다.  
당시 Stack내용은 다음과 같았으며 probe를 통해서 확인되었다.  
  
&nbsp;[#M\_더보기|접기|sun.misc.Unsafe.park ( native code )  
java.util.concurrent.locks.LockSupport.park ( unknown source )  
java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt ( unknown source )  
java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued ( unknown source )  
java.util.concurrent.locks.AbstractQueuedSynchronizer.acquire ( unknown source )  
java.util.concurrent.locks.ReentrantLock$NonfairSync.lock ( unknown source )  
java.util.concurrent.locks.ReentrantLock.lock ( unknown source )  
com.digicap.metv.PIM.data.CharacterList.Lock ( CharacterList.java:38 )  
com.digicap.metv.PIM.GarbageCollector.GarbageCollector.process ( GarbageCollector.java:84 )  
com.digicap.common.simple.serverManager.Caller.execute ( Caller.java:13 )  
org.quartz.core.JobRunShell.run ( JobRunShell.java:216 )  
org.quartz.simpl.SimpleThreadPool$WorkerThread.run ( SimpleThreadPool.java:549 )   
  
  
sun.misc.Unsafe.park ( native code )  
java.util.concurrent.locks.LockSupport.park ( unknown source )  
java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt ( unknown source )  
java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued ( unknown source )  
java.util.concurrent.locks.AbstractQueuedSynchronizer.acquire ( unknown source )  
java.util.concurrent.locks.ReentrantLock$NonfairSync.lock ( unknown source )  
java.util.concurrent.locks.ReentrantLock.lock ( unknown source )  
com.digicap.metv.PIM.data.CharacterList.Lock ( CharacterList.java:38 )  
com.digicap.metv.PIM.Serialize.Serializer.write ( Serializer.java:183 )  
com.digicap.metv.PIM.Serialize.Serializer.process ( Serializer.java:88 )  
com.digicap.common.simple.serverManager.Caller.execute ( Caller.java:13 )  
org.quartz.core.JobRunShell.run ( JobRunShell.java:216 )  
org.quartz.simpl.SimpleThreadPool$WorkerThread.run ( SimpleThreadPool.java:549 )   
\_M#]  
  
예제 코드 ( 클래스내 일부 )[#M\_더보기|접기|  
  
&nbsp;&nbsp; private transient ReentrantLock lock = new ReentrantLock(true);  
&nbsp;&nbsp;&nbsp; public boolean Lock(String info){  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; int r=1;  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; try {  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; while(lock.tryLock(6000, TimeUnit.MILLISECONDS)==false){  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; lockLog.error(Thread.currentThread().getName() + "| " + info + " | Can't aquire Lock | " + r);  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; if(r \>= 10){  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; return false;  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; }  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; ++r;  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;   
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; }  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; lockLog.debug(Thread.currentThread().getName() + " | " + info +" | Lock" );  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; return true;  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; } catch (InterruptedException e) {  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; lockLog.error(Thread.currentThread().getName() + "| " + info + " | Interrupted :" + e.getMessage() +" | " + r);  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; e.printStackTrace();  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; return false;  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; }  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;   
&nbsp;&nbsp;&nbsp; }  
&nbsp;&nbsp;&nbsp; public void Unlock(String info){  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; if(lock.isLocked()==true){  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;   
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; lock.unlock();  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; lockLog.debug(Thread.currentThread().getName() +" | " + info + " | Unlock");  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; }  
&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;   
&nbsp;&nbsp;&nbsp; }\_M#]  
