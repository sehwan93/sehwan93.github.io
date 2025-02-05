---
layout: post
title: "[JAVA] Executors.newVirtualThreadPerTaskExecutor() 명시적 선언"
tags: [JAVA]
categories: [java]
comments: true
---

웹플럭스로 작성되어있던 소스를 가상스레드로 컨버팅 하는 작업 중이였다.

사실 병렬처리로 인해 성능상 차이가 발생할 만큼의 API가 많이 존재하지 않기에 대부분 이슈가 없었으나

하나의 API에서 문제가 발생.

API 자체도 호출량이 많은데 해당 API 호출 시 20개정도의 API를 병렬로 호출해서 가공해서 데이터를 내려주는 API였다.

호출량이 많아지니까 레이턴시가 웹플럭스보다 2배가량 느려지기 시작. 원인을 찾아 헤맸다

-----

# 기존 소스

```
    try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
        CompletableFuture<responseDto> firstApiResponse = CompletableFuture.supplyAsync(() ->
             service.getApi(reqDto));

        try {
            CompletableFuture<Void> allOfFuture = CompletableFuture.allOf(
                firstApiResponse
            );
            
            반복

            allOfFuture.get(5000, TimeUnit.MILLISECONDS);

            response.setResponse(firstApiResponse.get());
            반복
        } catch (Exception e ) {
            return response;
        } finally {
            executor.shutdown();
        }
    }
```

newVirtualThreadPerTaskExecutor를 만들지만 CompletableFuture 에다가 전달을 하지 않고있었다.

supplyAsync를 사용하면 기본적으로 forkJoinPool을 사용.

java 21의 경우는 forkJoinPool을 또한 내부적으로 가상스레드를 활용할 가능성이 있지만 ForkJoinPool 자체는 여전히 CPU 코어 개수 기반의 제한된 워커 스레드를 사용.


-----

# 변경 소스

```
    try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
        CompletableFuture<responseDto> firstApiResponse = CompletableFuture.supplyAsync(() ->
             service.getApi(reqDto), executor); // 명시적으로 가상 스레드 전달

        try {
            CompletableFuture<Void> allOfFuture = CompletableFuture.allOf(
                firstApiResponse
            );
            
            반복

            allOfFuture.get(5000, TimeUnit.MILLISECONDS);

            response.setResponse(firstApiResponse.get());
            반복
        } catch (Exception e ) {
            return response;
        } finally {
            executor.shutdown();
        }
    }
```

위와 같이 명시적으로 가상스레드를 전달하도록 수정하니 성능 개선 완료. 하지만 여전히 webflux 보다는 성능이 좋지는 않다.

thread pin 현상 때문일 것으로 추측 중. 
