---
layout: post
title: "JavaAssist를 이용한 Dynamic Proxy 예제"
category: java
keywords: 
tags: 
published: 2013-05-13T18:42:04+09:00
modified: 2013-05-13T18:42:04+09:00
---
JavaAssist를 이용한 Dynamic Proxy 예제

간단하게.

{% highlight java linenos %}

public Object CreateMBeanProxy(Object inst, String proxyClassName) throws NotFoundException, CannotCompileException, InstantiationException, IllegalAccessException, ClassNotFoundException {

try {

ClassPool classPool = ClassPool.getDefault();

CtClass originClass = classPool.get(inst.getClass().getName());

log.debug(inst.getClass().getName());

CtClass proxyInterface = classPool.makeInterface(proxyClassName + "MBean");

CtClass proxyClass = classPool.makeClass(proxyClassName);

/* 인스턴스를 delicate 할 맴버 변수 생성 */

proxyClass.addField(CtField.make("private " + inst.getClass().getName() + " obj;", proxyClass));

/* 인터페이스, 클래스 메소드 복제 시작 */

CtMethod[] originCtClassMethods = originClass.getMethods();

for (CtMethod method : originCtClassMethods) {

/* 기본 메소드 제외 */

if (method.getName().equals("wait") || method.getName().equals("notifyAll") || method.getName().equals("notify") || method.getName().equals("getClass") || method.getName().equals("clone") || method.getName().equals("hashCode")

|| method.getName().equals("finalize") || method.getName().equals("equals")) {

continue;

}

log.debug(method);

/* 인터페이스 생성 */

proxyInterface.addMethod(CtNewMethod.abstractMethod(method.getReturnType(), method.getMethodInfo().getName(), method.getParameterTypes(), method.getExceptionTypes(), proxyInterface));

/* 클래스 메소드 본문 생성 */

String body = "{}";

if (method.getReturnType() == null) {

body = "{ obj." + method.getName() + "($$);}";

} else {

body = "{ return obj." + method.getMethodInfo().getName() + "($$);}";

}

/* 클래스 메소드 생성 */

proxyClass.addMethod(CtNewMethod.make(method.getReturnType(), method.getMethodInfo().getName(), method.getParameterTypes(), method.getExceptionTypes(), body, proxyClass));

}

/* 인터페이스, 클래스 메소드 복제 종료 */

log.debug("public " + proxyClassName + "(" + inst.getClass().getName() + " origin){ this.obj=origin;}");

proxyClass.addMethod(CtMethod.make("public void setOrigin(" + inst.getClass().getName() + " origin){ this.obj=origin;}", proxyClass));

proxyClass.setInterfaces(new CtClass[] { proxyInterface });

Class<?> runtimeProxyClass = proxyClass.toClass(inst.getClass().getClassLoader(), null);

Object proxyInstance = runtimeProxyClass.newInstance();

try {

Method mm = proxyInstance.getClass().getMethod("setOrigin", inst.getClass());

mm.invoke(proxyInstance, inst);

} catch (Exception e) {

// TODO Auto-generated catch block

e.printStackTrace();

}

return proxyInstance;

} finally {

}

}

{% endhighlight %}

