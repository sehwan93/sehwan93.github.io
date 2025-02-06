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


위의 소스에서 Thread.currentThread().getName() 을 호출 할 경우 

Thread[#107,ForkJoinPool.commonPool-worker-1,5,VirtualThreads]

위와 같은 이름이 나온다.

해당 이름만 보고 버츄얼스레드로 돌고 있다고 착각. 

챗GPT에 따르면 VirtualThreads가 들어간 이유는 해당 스레드는 ForkJoinPool의 스레드지만 해당 환경이 Java21 환경이므로

ForkJoinPool의 스레드가 가상스레드를 실행하는 플랫폼 스레드의 역할도 할 수 있고 그렇기 때문에 저렇게 이름이 나온 것.
(Thread.isVirtual()을 호출 할 경우 false가 나오긴 하더라.. 제대로 확인 안한 내 잘못이다)

아래는 챗GPT의 답변


🔥 예제 코드로 확인해보자
```
public class VirtualThreadExample {
    public static void main(String[] args) {
        Runnable task = () -> {
            Thread current = Thread.currentThread();
            System.out.println("Thread Name: " + current.getName());
            System.out.println("Is Virtual Thread? " + current.isVirtual());
        };

        // 가상 스레드 실행
        Thread vThread = Thread.ofVirtual().start(task);
    }
}
```

📌 실행 결과 예시:
Thread Name: VirtualThread[#25]/runnable@ForkJoinPool-1-worker-3
Is Virtual Thread? true

📌 이 결과가 의미하는 것:
ForkJoinPool-1-worker-3: 이건 원래 ForkJoinPool에서 실행되는 플랫폼 스레드임.
하지만 이 위에서 가상 스레드(VirtualThread[#25])가 실행됨.
즉, ForkJoinPool의 스레드가 일반적인 플랫폼 스레드 역할도 하고, 가상 스레드를 실행하는 역할도 함.

🚀 결론
✅ ForkJoinPool의 스레드는 원래 ForkJoinPool의 작업을 실행하는 플랫폼 스레드 역할을 하면서, 가상 스레드를 실행하는 플랫폼 스레드 역할도 할 수 있다.
❌ ForkJoinPool이 직접 가상 스레드를 만드는 건 아님!
✅ 하지만 가상 스레드가 실행될 때 ForkJoinPool의 스레드 위에서 실행될 수 있음!


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

참고 : https://waterfogsw.tistory.com/72
참고 : https://wedul.tistory.com/726

