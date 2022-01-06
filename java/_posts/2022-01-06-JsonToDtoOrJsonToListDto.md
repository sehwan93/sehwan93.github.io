---
layout: post
title: "[JAVA] Java Map To Dto / List<Map> To List<Dto> 또는 Json to Map / JsonArray to List "
tags: [JAVA]
categories: [java]
comments: true
---

웹 개발자라면 이제 뗄레야 뗄 수 없는 Json.

Json으로 받아온 데이터들을 보통 Map,List<Map>이나 DTO,List<Dto> 로 많이 컨버팅 해서 사용한다.

또한 json을 map으로 받아온 데이터를 Dto로 변환해서 사용하는 경우도 있는데

매번 사용하지만 매번 깜빡하고 예전에 작성했던 소스를 찾아보기에 기록.


* 사용 라이브러리
```java
 import com.fasterxml.jackson.databind.ObjectMapper
```

* 선언
```java
 ObjectMapper mapper = new ObjectMapper();
```

* Map TO Dto
 
```java
 Dto result = mapper.convertValue(testMap,Dto.class);
```

* JSON TO Dtp(Map)
```java
 Dto result = mapper.readValue(jsonStrong,Dto.class);
 Map<String,Object> result = mapper.readValue(jsonStrong,Map.class);
```

* List<Map> TO List<Dto>
```java
 List<Dto> result = mapper.convertValue(testList,Mapper.getTypeFactory().defaultInstanct().constructCollectionType(List.class, Dto.class));
```

* JsonArray TO List<Dto>
```java
 List<Dto> result = mapper.readValue(testString,Mapper.getTypeFactory().defaultInstanct().constructCollectionType(List.class, Dto.class));
 List<Map<String,Object>> result = mapper.readValue(testString,Mapper.getTypeFactory().defaultInstanct().constructCollectionType(List.class, Map.class));
```

readValue와 convertValue의 차이는 
readValue는 String을 변환해주고 convertValue는 Object를 다른 Object로 변경해준다.