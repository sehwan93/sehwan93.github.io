---
layout: post
title: "[Spring/java] Spring Schduler 사용법"
tags: [JAVA,Spring]
categories: [java]
comments: true
---

업무 중 작은 이슈가 하나 발견되었다.

내가 담당하고 있는 업무에서 회원 가입 약관을 우리 Mall 페이지에서 REST로 가져오는데.

Mall 이 서버가 다운되면 약관을 가져오지 못해서 내 업무에서도 회원 가입을 못한다는 것.

약관을 못 가져오면 안 띄워버릴 순 없으므로.. 스케쥴로 서버 메모리에 올려놓기로 결정.

그래서 사용하기로 한게 간단한 Spring Schduler

Java 버전 : 1.6
Spring 버전 : 3.0.5

## Spring Schduler 사용

1.xml파일 추가하기

context-scheduler.xml 파일을 추가했다. 기존 xml에 추가해도 되지만 파일을 새로 만드는게 관리하는데 편할 것 같아서 추가.

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:task="http://www.springframework.org/schema/task"
		xsi:schemaLocation="http://www.springframework.org/schema/beans
							http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
							http://www.springframework.org/schema/task
							http://www.springframework.org/schema/task/spring-task-3.0.xsd">
							<task:annotation-driven />
	</beans>
```


2.스케쥴을 정의 할 Class 파일 생성

```java
	@Service
	public class SchedulerService {
		
		
		 @Scheduled(fixedDelay=1000)
		    public void TestScheduler(){
		        System.out.println("테스트입니다");
		    }
	}
```

위의 코드처럼 @Scheduled 어노테이션이 걸린 메소드가 @Scheduled 어노테이션에 준 파라미터에 따라서 주기적으로 실행된다.
@Scheduled 어노테이션 파라미터로는 3가지 값이 들어갈 수 있다.
 

1.cron : cron은 리눅스 cron 표현식과 동일하게 설정
2.fixedDelay : 이전 실행된 작업 종료 시간으로 부터 정의된 시간 만큼 지난 후 실행 ( 밀리세컨드 단위 )
3.fixedRate : 이전에 실행된 작업 시작시간으로 부터 정의된 시간만큼 지난 후 실행 ( 밀리세컨드 단위 )



