---
layout: post
title: "[JavaScript/Jquery]Input과 keyup을 이용한 테이블 실시간 검색"
description: "[JavaScript/Jquery]Input과 keyup을 이용한 테이블 실시간 검색"
tags: [JavaScript,Jquery]
categories: [javascript]
comments: true
---

input과 keyup 펑션을 이용한 실시간 테이블 검색 기능 입니다.

## JS를 통한 Table 실시간 검색

기능 설명 : Input에 값을 입력하면 해당 값이 있는 행만 표시해주는 코드입니다.

### 주석(설명) 없는 원본 코드

```javascript
$("#inputSearchText").keyup(function(){
	var searchText = $(this).val();
	$("#tableTarget > tbody > tr).hide();
	var temp = $("#tableTarget > tbody > tr > td:contains('"+ searchText +"');
	$(temp).parent().show();
}
```


### 주석(설명) 있는 코드

```javascript
//input에 keyup 이벤트 등록
$("#inputSearchText").keyup(function(){
	//keyup 이벤트 발생 시 해당 input의 value 가져오기.
	var searchText = $(this).val();
	//실시간 검색이 필요한 table의 모든 행(tr) 숨김 처리
	$("#tableTarget > tbody > tr).hide();
	//해당 table에서 input에 입력한 데이터가 있는 td Element 찾기.
	var temp = $("#tableTarget > tbody > tr > td:contains('"+ searchText +"');
	//입력한 데이터가 있는 Elemnet의 부모 Elemnet(td)만 표시.
	$(temp).parent().show();
}
```