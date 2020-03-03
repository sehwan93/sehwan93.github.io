---
layout: post
title: "[Spring/java] LifecycleProcessor not initialized - call 'refresh' before invoking lifecycle methods via the context"
date: 2020-03-03 22:50:00 +0300
description: [Spring/java] LifecycleProcessor not initialized - call 'refresh' before invoking lifecycle methods via the context
tags: [JAVA,Spring]
categories: [java]
comments: true
---

# [Spring/java] LifecycleProcessor not initialized - call 'refresh' before invoking lifecycle methods via the context 에러

개발환경을 새로 세팅해야 하는 상황이 발생해서 개발환경 파일을 그대로 복사해서

붙여넣고 서버를 올렸다..

한번에 될거란 생각은 1도 안했지만 

어김없이 에러가 발생.

MAVEN Setting 파일이 default 파일로 되있어서 MAVEN 에러가 발생해서 하나 잡고..

이제 되겠거니 하고 서버를 올렸더니

```
	LifecycleProcessor not initialized - call 'refresh' before invoking lifecycle methods via the context 
```

에러가 발생했다.

구글링을 해보니 

Spring 버전이 맞지 않아 발생 할 수 있다는 글을 발견..

[spring 버전차이](http://blog.naver.com/PostView.nhn?blogId=hwanwhat81&logNo=220755820276&parentCategoryNo=&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView)

Maven Clean

Maven Install 을 먼저 해보려고 했더니

또 다시 발생한 에러..

```
org.apache.maven.plugin.CompilationFailureException: Compilation failure
        Unable to locate the Javac Compiler in:
        [개인 로컬의 JAVA_HOME]\jre6\..\lib\tools.jar
        Please ensure you are using JDK 1.4 or above and
        not a JRE (the com.sun.tools.javac.Main class is required).
        In most cases you can change the location of your Java
        installation by setting the JAVA_HOME environment variable.
```

메이븐에서 Library를 제대로 받아오지 못하고 있는 것 같다.

아무래도 이걸 해결하면 될 듯 싶어서 다시 구글링을 해보니

발견한 글

[JRE_HOME](https://bboks.net/281)

JDK 설정 문제로 MAVEN Library를 제대로 받아오지 못하고 있었던 것 같다.

기존 환경과 동일하게 JDK를 설정하고 MAVEN Install 하니 정상 작동.

MAVEN Intall을 성공 시키고

서버를 올리니 서버도 정상적으로 올라갔다.



*정리*
1. LifecycleProcessor not initialized - call 'refresh' before invoking lifecycle methods via the context 에러 발생
2. 위의 에러는 Spring 버전 차이로 발생할 수 있음
3. MAVEN Intall 하여 제대로 된 Library 받아오려고 시도
4. JDK 설정 문제로 MAVEN Intall이 제대로 되지 않음
5. JDK 설정을 제대로 하고 난 후 MAVEN Intall
6. MAVEN Intall 성공 후 서버 올리니 해결!



