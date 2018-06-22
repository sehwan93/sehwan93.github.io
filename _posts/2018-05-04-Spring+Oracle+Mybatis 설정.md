---
layout: post
title: "Spring+Oracle+Mybatis 설정"
img: 
date: 2018-05-04 15:00:00 +0300
description: Spring Oracle Mybatis 연동 설정
tag: [Spring Oracle Mybatis]
category: Spring
---

#Spring + Oracle + mybatis 연동

설정은 자주 할 필요가 없다.

그래서 대부분 외우지 않고 하는 방법을 알아만 뒀다가 자세한 것들은 검색해서 해결하는 편이다.

요즘은 STS로 프로젝트를 만들기만 하면 Spring설정은 대부분 되어있어서 추가적인 설정만 하면 된다.

하지만 mybatis는 사용자가 직접 추가해주어야 한다. 특히나 mysql은 바로 dependency만 추가하면 가능하지만 oracle은 repository까지 추가해주어야 해서 더욱 번거롭다.

그래서 매번 찾는 수고를 덜기 위해 기록해두고자 한다.

- Spring LegacyProject 만들기
- 메이븐 설정 (pom.xml)
```xml

	<!--dependencies 위에 설정 -->
    <repositories>
		  <repository>
		   <id>oracle</id>
		   <name>ORACLE JDBC Repository</name>
		   <url>https://code.lds.org/nexus/content/groups/main-repo</url>
		  </repository>
	</repositories>
    
	<!--dependencies 안쪽에 추가-->
		<!-- mybatis-spring -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.2.3</version>
        </dependency>
 
        <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.3.0</version>
        </dependency>
 
        <!-- spring jdbc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${org.springframework-version}</version>
        </dependency>
        
        <!-- ojdbc6 -->
		<dependency>
		 <groupId>com.oracle</groupId>
		 <artifactId>ojdbc6</artifactId>
		 <version>11.2.0.3</version>
		</dependency>
```
- root-context.xml에 namespaces 추가
 aop, mybatis-spring, context, jdbc 4개 추가
 
- root-context.xml에 bean 설정

```xml
	<bean class="org.springframework.jdbc.datasource.DriverManagerDataSource"
        id="dataSource">
        <property value="oracle.jdbc.driver.OracleDriver" name="driverClassName" />
        <property value="jdbc:oracle:thin:@localhost:1521:XE"
            name="url" />
        <property value="db이름" name="username" />
        <property value="db비밀번호" name="password" />
    </bean>

	<bean class="org.mybatis.spring.SqlSessionFactoryBean" id="SqlSessionFactory">
	    <property name="dataSource" ref="dataSource" />
	    <property value="classpath:mybatis-config.xml" name="configLocation" />
	    <property value="classpath:/mappers/**/*Mappers.xml" name="mapperLocations" />
	</bean>
	
	<bean class="org.mybatis.spring.SqlSessionTemplate" id="sqlSession" destroy-method="clearCache">
  	  <constructor-arg name="sqlSessionFactory" ref="SqlSessionFactory" />
	</bean>
```

- mybatis-config.xml 작성

```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>

    </configuration>
```

- mappers 패키지 및 mapper.xml 작성

 - resources 파일 및에 mappers 패키지 작성
 - mapper.xml파일 생성
 
```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="DAO에서 선언할 namespace">

    </mapper>
```