---
layout: post
title: "[ORACLE] 특정 테이블에 없는 데이터 조인으로 조회하기"
categories: [ORACLE]
comments: true
---

# 특정 테이블에 없는 데이터 조인으로 조회하기

사은품이 지급 되지 않은 주문 데이터를 뽑아야 하는 상황이 생겼다.

처음엔 그냥 NOT IN 을 하면 되겠지 생각.

~~~
SELECT  *
FROM    ORDER O
WHERE   GIFT_NO NOT IN (
                            SELECT GIFT_NO
                            FROM   GIFT
                       )   
~~~

이런 느낌?

근데 확인해보니 키가 두개다. 제길. 조인으로 풀어야했다.

조인 조건을 아우터로 걸어놓고 키 값이 널인 데이터를 찾도록 했다.


~~~
SELECT  *
FROM    ORDER O,GIFT G
WHERE   O.GIFT_NO  = G.GIFT_NO(+)
AND     O.GIFT_SEQ = G.GIFT_SEQ(+)
AND     G.GIFT_NO IS NULL
~~~

아우터 조인을 통해 ORDER 에는 데이터가 있으나 GIFT 에는 데이터가 없는 데이터까지 전부를 뽑아낸다.
단순 조인을 하면 GIFT 테이블에 데이터가 없는 데이터는 안나오기 때문에 아우터로 걸어야 한다. 

그 중 GIFT 테이블의 PK 가 없는 데이터를 걸러내는 방식.
