---
layout: post
title: "jmeter java request 클래스 쉽게 만들기"
category: blog
keywords: 
tags: 
published: 2012-07-18T13:08:34+09:00
modified: 2012-07-18T13:08:38+09:00
---
Jmeter를 이용하여 성능 테스트를 할 때,&nbsp;

기본적인 http가 아닌 다른 유형의 요청을 하고 싶은 경우에는 &nbsp;별도의 클래스를 만들어야 한다.

본인은 MongoDB의 성능을 측정이 필요 하였다.

기본적인 코드 작성은&nbsp;[http://www.javajigi.net/pages/viewpage.action?pageId=184](http://www.javajigi.net/pages/viewpage.action?pageId=184)&nbsp; 을 참고 하였으며 좀 더 편하게 하기 위해서 걍 내 나름 대로 작성하였다.

순서는 다음과 같다.

1.&nbsp;Jmeter 다운로드

2. 이클립스 Java 프로젝트 생성, lib라는 폴더 생성

3. lib 폴더에 있는 모든 라이브러리를 lib라는 폴더에 복사, 모든 Jar 클래스 패스에 추가

4. 내 나름의 클래스 만들기

**5. Jmeter의 lib 폴더에 내가 사용하는 라이브러리 파일들( log4j등 )넣기&nbsp;**

**&nbsp;\*\* 안 넣은 경우 에러도 안 내보내고 실행도 안됨 \*\***

6.프로젝트에서 완성된 소스를 jar로 추출

7.jar를 Jmeter의&nbsp;lib/ext 폴더에 복사한 이후 Jmeter실행

라이브러리를 Eclipse내에서 만든 java&nbsp;프로젝트에다가 몽땅 추가

[##\_1C|cfile7.uf.145E5542500620EA2DBC04.png|width="303" height="241" filename="eclipse-jar 몽땅 추가.png" filemime="image/jpeg" style="text-align: center; "|\_##]

빌드 패스에 추가 한다.

[##\_1C|cfile25.uf.1730A93550062095133538.png|width="640" height="476" filename="eclipse설정.png" filemime="image/jpeg" style=""""|\_##]

package com.digicap.mongo.test;

import java.net.UnknownHostException;

import java.util.HashMap;

import org.apache.jmeter.protocol.java.sampler.AbstractJavaSamplerClient;

import org.apache.jmeter.protocol.java.sampler.JavaSamplerContext;

import org.apache.jmeter.samplers.SampleResult;

import com.digicap.xpg.mongodb.MongoDBConnector;

import com.digicap.xpg.mongodb.MongoDBConnectorDetail;

import com.mongodb.MongoException;

/\*\*

&nbsp;\* 샘플 클래스

&nbsp;\* @author dosuser

&nbsp;\*

&nbsp;\*/

public class SamplerExample extends AbstractJavaSamplerClient {

@Override

public SampleResult runTest(JavaSamplerContext arg0) {

SampleResult results = new SampleResult();

System.out.println("runTest");

try {

// Record sample start time.

results.sampleStart();

MongoDBConnectorDetail connector = new MongoDBConnectorDetail();

connector.connect("localhost:27018","test",false);

//연결후 &nbsp;블라블라

results.setSuccessful(true);

}catch (Exception e) {

getLogger().error("SleepTest: error during sample", e);

results.setSuccessful(false);

} finally {

results.sampleEnd();

}

System.out.println("runTest fin");

return results;

}

}

