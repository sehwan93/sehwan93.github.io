---
layout: post
title: "[JAVA/redis] 레디스 병렬처리 해도 될까? HashMap 그리고 ParallelStream UnknownRedisException "
tags: [JAVA/Redis/thread]
categories: [java]
comments: true
---

1년에 한번 하는 블랙프라이데이 같은 행사가 있다.

트래픽이 몰리고.. 지연이 생기고.. 지연구간을 찾는 중에 DB는 병렬로 처리하고 있지만 redis에서 캐시로 가져오는 부분은 병렬처리가 안되어 있는 것을 제보받았다.


-----

## 왜 병렬처리를 안해놓았을까? ( 레디스는 싱글스레드니까 미들웨어에서도 병렬처리를 할 필요가 없다? )

처음 개발한게 내가 아니긴 하지만 redis는 워낙 빠르게 응답하기에 굳이 병렬처리를 하지 않아도 충분히 빠른 속도를 보장하니 그런 듯 하였다.

조금 더 확인해보니 redis의 대표적 특징 중 하나가 레디스 자체가 싱글스레드 라는 것.

레디스가 싱글스레드니까.. 미들웨어에서 병렬처리를 해도 효과가 없으니 안해놓은걸까? 라는 생각을 가졌지만

생각해보면 레디스는 이미 여러 시스템에서 요청을 받고있을탠데 그 요청들을 병목현상 없이 처리하고 있으니.. 직접 적용해보는걸로!



----

## 병렬처리 결과 속도의 향상이 있었다.

**결론부터 말하자면 눈에보이는 속도 향상이 있었다.**

이슈가 됐었던 시스템 자체가 트래픽이 상당히 많은 시스템인데 P50 P70 P90 P95 까지 10%이상 레이턴시가 줄었고

특히나 레이턴시 자체가 들쭉날쭉 하던게 굉장히 많이 사라졌다.

하지만 병렬처리는 언제나 신중해야 하는법.. 여지없이 익셉션이 발생했다

코드는 대충 설명하자면 리스트를 루프를 돌면서 레디스에 값이 있으면 Map에 넣고 없으면 쿼리를 조회하는 코드이다.

기존의 이터레이터 코드

```
Iterater<dto> iter = list.iterater();
HashMap<dto> hashMap = new HashMap<>();
while(iter.hasNext()) {
    if("xx".eqauls(iter.next().get("A"))) {
        hashMap.put("A","A");
        iter.remove();
    }
}
-- 이 아래는 캐쉬 없는 값들 쿼리 조회
```

변경한 병렬처리 코드
```
list.parallelStream().forEach(a -> {
    if("xx".eqauls(iter.next().get("A"))) {
        hashMap.put("A","A");
    }
});
-- 이 아래는 캐쉬 없는 값들 쿼리 조회
```

----

## 이슈1. 병렬처리 작업 중에는 HashMap은 사용하면 안된다.

parallelStream을 사용하니까 100개중에 1~2개정도가 Map에 Put을 못하는 경우가 발생.

HashMap은 thread-safe하지 못하여 put을 못하는 케이스.

해결 : ConcuurentHashMap 으로 대체하니 해결완료.


----

## 이슈2. UnknownRedisException

Unknown redis exception; nested exception is java.util.concurrent.RejectedExecutionException: Thread limit exceeded replacing blocked worker

hashMap을 처리하고 나니 트래픽이 몰릴 경우 위와 같은 이슈가 발생.
( 참고 : https://stackoverflow.com/questions/54403451/lettuce-rediscache-throws-java-util-concurrent-rejectedexecutionexception-thread)

병렬로 레디스를 호출하다 보니 명령완료가 기다리는 스레드가 쌓일 경우 해당 익셉션이 발생할 수 있다고한다.

우선 캐시는 필수가 아니므로 익셉션 발생 시 기존 로직을 태우도록 임시로 방어처리를 했다.

java21 프로젝트라서 가상스레드로 변경해볼 예정!


