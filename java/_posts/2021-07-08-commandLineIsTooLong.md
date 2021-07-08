---
layout: post
title: "[IntelliJ] IntelliJ 빌드,실행 시 Command line is too long. Shorten command line for ... 에러"
tags: [JAVA,IntelliJ]
categories: [java]
comments: true
---

환경

프레임워크 : Spring Boot 2.3.1
빌드툴 : gradle
개발환경 : IntelliJ

Spring Boot로 개발되어있는 프로젝트를 clone 받은 후 gradle 빌드까지 무난하게 성공.

run configuration 에 메인 클래스 설정하고 실행을 눌렀으나 역시나 발생하는 에러

## Command line is too long. Shorten command line for

![error](/assets/img/20210708/1.jpg)

```
Command line is too long. Shorten command line for {프로젝트명} or also for Spring Boot default configuration?
```

과거에 spring boot 뿐만 아니라 spring 프로젝트 실행에서도 봤던 에러.



# 해결방법

1. workspace.xml 파일 변경
1. Run Configuration에서 Shorten command line 변경


### 1. workspace.xml 파일 변경 방법
 
> 파일 위치 : {프로젝트 루트 경로}/.idea/workspace.xml  

1. {프로젝트 루트 경로}/.idea/workspace.xml 파일 오픈
1. PropertiesComponent
1. PropertiesComponent 태그 안에 \<property name="dynamic.classpath" value="true" /> 태그 추가.


```xml
예시)
<component name="PropertiesComponent">
    <property name="dynamic.classpath" value="true" /> <!-- 이 태그 추가 -->
    <property name="RunOnceActivity.OpenProjectViewOnStart" value="true" />
    <property name="RunOnceActivity.ShowReadmeOnStart" value="true" />
    <property name="WebServerToolWindowFactoryState" value="false" />
    <property name="aspect.path.notification.shown" value="true" />
    <property name="last_opened_file_path" value="$PROJECT_DIR$/java/_posts" />
</component>
```

### 2. Run Configuration에서 Shorten command line 변경

1번 방법으로 했으나 실패하여 2번 방법으로 해결.

> Shorten command line 설정 값 변경 
>> none => JAR manifest

![solve](/assets/img/20210708/2.jpg)


 



