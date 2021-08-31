---
layout: post
title: "[SptringBoot] Spring Boot 내장 톰캣 이용한 Jar 실행 방법(포트 변경) 및 80포트 Permission denied 에러"
tags: [JAVA,IntelliJ]
categories: [java]
comments: true
---

 Spring Boot는 톰캣이 내장되어있기 때문에 서버에 따로 Tomcat을 구성하지 않아도 jar 파일만을 통해서 실행 할 수 있다. 
 일반적인 jar 프로그램 실행과 동일한 방법으로 실행하면 된다
 
* SpringBoot jar 파일 실행 방법
```linux
 java -jar [이름].jar
```

 다만 위와 같이 실행 할 경우 테스트용 포트인 8080 포트가 기본 할당된다. 포트를 변경하는 방법은
 
1. application.properties 파일 변경
```aidl
 server.port = 80
```


2. 명령어 실행시 Derver.port 옵션 추가
```aidl
 java -Dserver.port=80 -jar demo-0.0.1-SNAPSHOT.jar
```




---

### 80포트 Permission denied

 일반적으로 Linux에서는 0~1023 포트는 root 사용자만 사용 할 수 있다. 그렇기 때문에 해결방법은
 
1. root로 접속하여 실행한다.
2. 8080포트를 80포트로 redirect 시킨다.