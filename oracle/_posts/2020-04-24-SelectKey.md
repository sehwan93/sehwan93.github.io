---
layout: post
title: "[Oracle/iBatis/Java] SelectKey Insert 후에 Key 값 바로 가져오기"
description: [Oracle/iBatis/Java] SelectKey Insert 후에 Key 값 바로 가져오기
tags: [Oracle/Java]
categories: [oracle]
comments: true
---

[Oracle/iBatis/Java] SelectKey Insert 후에 Key 값 바로 가져오기

## SelectKey 

개발을 하다보면 

INSERT  =>  특정 로직 수행  => UPDATE 

이런 형태로 구현을 해야 할 때가 있다.

나의 경우 PUSH 서버를 구축하는데 필요했는데.

DB에 발송 정보를 먼저 INSERT 하고 (INSERT)

FireBase를 호출하여 PUSH를 발송하고 (특정 로직 수행) 

해당 결과 값을 받아서 위에서 INSERT 한 데이터에 메시지 ID나 멀티캐스트 ID를 업데이트 해주고자 하였다. (UPDATE)


물론 해당 로직을 하기 위해서 java에서

SELECT 하여 KEY 값을 조회 후 값을 담기

=> 해당 KEY로 INSERT 수행

=> 로직 수행

=> 담아놨던 KEY로 UPDATE 수행

할 수도 있겠지만 코드는 쉽고 짧을 수록 좋은 법.


iBatis의 selectKey를 통해 Insert 하면서 동시에 key값을 받아오는 방법으로 처리 할 수 있다.


```sql
<insert id="insertUser" parameterClass="java.util.Map">
		<selectKey keyProperty="userCd" resultClass="Integer">
			SELECT SEQ_TEST.NEXTVAL FROM DUAL
		</selectKey>
		INSERT INTO USER(
				   	USER_CD,
				   	NAME
					)
			 VALUES (
					#userCd#,
					'테스트',
					)
	</insert>
```

위와 같이 XML 작성 하면 

iBatis 에서 return 값으로 selectKey의 resultClass Object를 반환한다.

