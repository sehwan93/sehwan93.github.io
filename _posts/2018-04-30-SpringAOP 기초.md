---
layout: post
title: "Spring AOP 기본."
img: 
date: 2018-04-30 14:00:00 +0300
description: 스프링 AOP를 오라클 DB와 연동하여 사용하는 예제입니다.
tag: [Spring, AOP, Oracle]
---
# Spring AOP란?

Spring AOP는 로깅, 권한체크, 보안과 같은 모든(또는 대부분) 코드에 들어가야 할 공통의 코드를
하나하나 모든 코드의 앞뒤에 붙이는 방식이 아닌. 라이브러리를 통해 한 번의 코드 작성으로 적용 시키는 것.

간단한 예제를 통해서 AOP의 개념과 사용법을 익혀보도록 하겠습니다.
### 주요 용어


| 용어 | 설명 |
|------|-----|
|Advice|실제 적용시키려고 하는 코드 자체, 로그 출력 기능, 보안 기능 등 적용 해야 할 자체는 Aspect라고 부르지만 실제 구현시에는 Advice를 제작한다고 표현|
|target|Aspect를 적용해야 하는 대상 객체|
|join points|작성된 Advice가 적용 될 수 있는 위치|
|PointCuts|여러 joinPoints 중에서 Adivce를 적용할 대상을 선택하는 정보|


### 사용 방법

1. pom.xml에 Properties 변경 및 Dependencies 추가
```xml
	<!-- properties 태그 -->
	<properties>
		<java-version>1.8</java-version>
		<org.springframework-version>4.3.8.RELEASE</org.springframework-version>
		<org.aspectj-version>1.8.9</org.aspectj-version>
		<org.slf4j-version>1.6.6</org.slf4j-version>
	</properties>
    
    <!--dependencies 태그 -->
    <!-- AspectJ -->
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>${org.aspectj-version}</version>
		</dependency>	
		
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjtools</artifactId>
			<version>${org.aspectj-version}</version>
		</dependency>	
		
		<!-- AOP -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>		
		
		<!-- tx -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
```
2. root-context.xml에 aop와 tx 네임스페이스 추가
3. 샘플용 테이블 제작 및 샘플 데이터 주입, Message 시퀸스 생성
```sql
	--message 테이블 시퀸스
	create sequence msg_seq 
    start with 1
    increment by 1;

    create table tbl_message (
        mno int,
        targetid varchar(50),
        sender varchar(50),
        message varchar(250),
        opendate timestamp,
        senddate timestamp default sysdate
    );

    create table tbl_user (
        userid varchar(50),
        userpw varchar(50),
        username varchar(50),
        userpoint int default 0
    );

    INSERT INTO tbl_user(userid,userpw,username) values ('user0','user0','홍길동');
    INSERT INTO tbl_user(userid,userpw,username) values ('user1','user1','김세환');
    INSERT INTO tbl_user(userid,userpw,username) values ('user2','user2','김연아');
    INSERT INTO tbl_user(userid,userpw,username) values ('user3','user3','박지성');
    INSERT INTO tbl_user(userid,userpw,username) values ('user4','user4','유병재');
```

4. VO,dao,Mapper, 서비스 작성
	*	MessageVO 및 UserVO 생성
    *	MessageDAO 생성
```java
    public interface MessageDAO {
    	//등록
        public void create(MessageVO vo) throws Exception;
        //읽기
        public MessageVO readMessage(Integer mid) throws Exception;
        //수정
        public void updateState(Integer mid) throws Exception;
    }
```

5. MessageDAO용 Mapper 제작
```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="org.sehwan.mapper.MessageMapper">
      
        <insert id="create">
            insert into tbl_message (targetid,sender,message) values
            (#{targetid},#{sender},#{message})
        </insert>   	

        <select id="readMessage" resultType="MessageVO">
            SELECT * FROM tbl_message WHERE mno=#{mno}
        </select>

        <update id="updateState">
            UPDATE tbl_message SET opendate = sysdate WHERE mno=#{mno}
        </update>	
    </mapper>
```

6. MessageDAOImpl 제작
```java
    package org.sehwan.dao;
    import org.apache.ibatis.session.SqlSession;
    import org.sehwan.vo.MessageVO;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Repository;

    @Repository
    public class MessageDAOImpl implements MessageDAO {

    @Autowired
    private SqlSession session;

    private static String namespace = "org.sehwan.mapper.MessageMapper";

    @Override
    public void create(MessageVO vo) throws Exception {
        // TODO Auto-generated method stub
        session.insert(namespace+".create",vo);
    }

    @Override
    public MessageVO readMessage(Integer mno) throws Exception {
        // TODO Auto-generated method stub
        return session.selectOne(namespace+".readMessage",mno);
    }

    @Override
    public void updateState(Integer mno) throws Exception {
        // TODO Auto-generated method stub
        session.update(namespace+".updateState",mno);
    }
}
```

7. 포인트 처리를 위한 PointDAO,Impl 및 Mapper구현
	MessageDAO 및 mapper에서 할 수도 있지만 구분하는 것이 로직을 파악하는데 더 용이 할 수 있으므로 분리하였습니다.

```java
	public interface PointDAO {
	public void updatePoint(String userid,int point) throws Exception;
	}
```

```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

       <mapper namespace="org.sehwan.mapper.pointMapper">

        <update id="updatePoint">
            update tbl_user set userpoint=userpoint+#{point} where userid=#{userid}
        </update>
    </mapper>
```

8. MessageService,Impl 구현
```java
	public interface MessageService {
        public void addMessage(MessageVO vo) throws Exception;
        public MessageVO readMessage(String userid,Integer mno) throws Exception;
	}
```

```java
	@Service
	public class MessageServiceImpl implements MessageService{
	
	@Autowired
	private MessageDAO messageDAO;
	
	@Autowired
	private PointDAO pointDAO;
	
	
	@Override
	public void addMessage(MessageVO vo) throws Exception {
    //Message 생성
    messageDAO.create(vo);
    //보낸 사람 id 추출하여 10포인트 지급
    pointDAO.updatePoint(vo.getSender(), 10);
	}
	
	@Override
	public MessageVO readMessage(String userid, Integer mno) throws Exception {
    	// 읽음 상태로 전환
    	messageDAO.updateState(mno);
    	//읽은 사람 5포인트 지급
   	 	pointDAO.updatePoint(userid, 5);
    	//메세지 조회 후 출력
    	return messageDAO.readMessage(mno);
	}

	}
```

### 본격적인 AOP제작
1. Advice 생성
	우리의 목적은 메세지를 만들고 읽을 때 마다 로그를 기록하고 실행에 걸린 시간을 로그에 기록하는 것! 
	aop기능을 위해서 aop패키즈를 작성하고 root-context.xml에 해당 패키지 인식하도록 설정.
    
    또한 root-context.xml에서 aop기능을 설정할 수 있도록 <aop:config>를 추가
```xml
	<context:component-scan base-package="org.sehwan.aop"></context:component-scan>
    <aop:config></aop:config>
```

2. SampleAdvice 작성

```java
	//스프링에서 bean으로 인식하기 위함
    @Component
    @Aspect
    public class SampleAdvice {
        private static final Logger logger = LoggerFactory.getLogger(SampleAdvice.class);

        //pointcuts을 지정하는 문법, AspectJ의 언어. org.sehwan.service.MessageService로 시작하는 모든 클래스의 모든 메소드를 지정.
        @Before("execution(* org.sehwan.service.MessageService*.*(..))")
        public void startLog() {
            logger.info("----------------");
            logger.info("----------------");
        }
        //제대로 지정된 후에는 STS에서 코드의 라인을 알려주는 곳 앞에 화살표가 표시!
}

```

3. Controller작성
```JAVA
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.stereotype.Component;

    //스프링에서 bean으로 인식하기 위함
    @Component
    @Aspect
    public class SampleAdvice {
        private static final Logger logger = LoggerFactory.getLogger(SampleAdvice.class);

        //pointcuts을 지정하는 문법, AspectJ의 언어. org.sehwan.service.MessageService로 시작하는 모든 클래스의 모든 메소드를 지정.
        @Before("execution(* org.sehwan.service.MessageService*.*(..))")
        public void startLog() {
            logger.info("----------------");
            logger.info("----------------");
        }
        //제대로 지정된 후에는 STS에서 코드의 라인을 알려주는 곳 앞에 화살표가 표시됨!
    }
```

4. 위의 메소드를 호출하면 log에 
INFO : org.sehwan.aop.SampleAdvice - ----------------
INFO : org.sehwan.aop.SampleAdvice - ----------------
위와 같은 로그가 기록됨!