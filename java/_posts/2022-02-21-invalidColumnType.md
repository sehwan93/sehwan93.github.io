---
layout: post
title: "[JAVA/Mybatis] invalid column type 1111 오류"
tags: [JAVA]
categories: [java]
comments: true
---

오늘도 여유롭게 코딩 중..은 무슨 "이렇게 개발하면 되겠다!" 머릿속에 떠올랐지만

IDE에서 바꿔야 할 부분을 찾아보니 서칭 되는 부분이 1..2..3....25 하. 개빡친다 이번에 고칠 때 통합시켜버리고 만다..

어디부터 손대야지.. 하... 개발하기 싫다.. 생각 할 때 오는 메신저.

"저장 버튼을 눌렀는데 오류 발생했다고 저장이 안되네요~"

-------

'혹시 내 잘못인가?'

'저번에 컬럼 추가했는데 그게 문제인가?'

'잘 되던게 갑자기??? 알고보니 안쓰던 기능이였나?' 

개발 하기도 싫었는데 이슈 트래킹 하는게 살짝 반가웠다. 케이스 확인해서 재현해보니까 나타나는 에러

-----

# Caused by: java.sql.SQLException: Invalid column type: 1111

SELECT 문에서 발생하는 문제였다.

INSERT 도 아니고 SELECT 하는데 타입이 잘못 됬다니. 이게 무엇인가.

디버깅을 통해 데이터를 확인해보니 조회 조건 중 일부에 NULL이 들어가고 있었다.

*WHERE ID = #{param} 이 상황에 파라미터에 NULL이 들어가면 WHERE ID = NULL로 조회를 해야 정상 아닌가..? 뭐지 이 에러..*

----

## ORACLE의 에러가 아닌 Mybatis에서 발생하는 에러.

**SQLException 이기에 오라클 에러인 줄 알았는데 구글링 결과 Mybatis 에러.**

Java에서 넘긴 파라미터 중 NULL이 있을 경우 발생한다고 한다.

### 아니 근데 분명히 다른데선 잘 돌아갔는데?

파라미터에 NULL을 허용 하는 방법이 따로 있었고. 당연하게 그렇게 해왔었는데. 그렇게 안되어 있는 코드가 있었다.

```
--NULL을 허용하지 않은 파라미터
#{param}

--NULL을 허용한 파라미터
NULL
#{param,jdbcType=VARCHAR}
```

---

## * 언제 그걸 다 붙이고 있냐 OR 우리 시스템은 그거 없어도 NULL 잘 들어가던데?

mybatis XML 설정 파일에 NULL을 허용하는 방법이 있다고 한다. ( 필자는 혼자 개발하는 시스템이 아니기에 적용하진 않았다. )

```
<settings> 
    <setting name="cacheEnabled" value="false" />
    <setting name="jdbcTypeForNull" value="NULL" /> 
</settings>
```





