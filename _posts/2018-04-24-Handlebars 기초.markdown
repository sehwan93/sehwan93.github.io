---
layout: post
title: "Handlebars"
img: 
date: 2018-04-24 14:49:00 +0300
description: Handelbars
tag: [Handelbars]
comments: true
---
# Handlebars

첫 번째 포스트!

첫 번째 포스트인 만큼 따로 정해진 양식 없이 막 써보겠습니다.


### Handlebars란?
handlebars는 javascript 템플릿 중 하나입니다.

우리가 보통 ajax를 사용하여 서버에서 데이터를 받아오면 HTML로 뿌려주기 위해선 다음과 같은 
코드를 작성합니다.
```javascript
	function getAllList(){
		$.getJSON("/replies/all/" + bno, function(data) {
			console.log(data.length);
			var str = "";

			$(data).each(
					function() {
						str += "<li data-rno='"+this.rno+"' class='replyLi'>"
								+ this.rno + ":" + this.replytext + "<button>MOD</button></li>";
					});

			$("#replies").html(str);
		});
	}
```
혹시 모르는 사람들이 있을까봐 간략한 설명을 하자면

/replies/all/ 이란 url에 요청을 하면 서버는 요청을 처리한 후 JSON 형식의 데이터를 반환한다.

function(data)에서 받아오는 data가 그 json 데이터!

json데이터를 하나하나 찾아가며 $(data).each(for문과 같은 역할)를 통해 str변수에

li문을 추가해가며.. html 코드와 this.rno와 같이 자신이 가지고있는 변수를 섞어가며 html코드를 

완성시킨 다음 변수 str(완성된 html구문)을 body에 뿌려주는 방식!


#### 너무 복잡하다!

"로 감싸고 html코드 쓰다가 '로 감싸고 this.변수로 데이터도 뽑아주고 다시 '쓰고 "감싸주고.. "'는 오타나면 찾기도 정말 힘들다. 

이러한 복잡함을 해결하기 위한 것이 handlebars와 같은 javascript template!


###어떻게 사용할까?
  
우선 사용하기 위해서는 jquery와 handlebars를 받아와야한다.

```javascript
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js"></script>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/3.0.1/handlebars.js"></script>
```
최소한의 사용법은 아래와 같은 코드를 body 안에 넣어주면 된다.
```javascript
	<script id="template" type="text/x-handlebars-template">
		<h1>{{name}}</h1>
		<h2>{{userid}}</h2>
	</script>
	<script>
		var source = $("#template").html();
		var template = Handlebars.compile(source);
		var data = {name:"김세환",userid:"sehwan"};
		$("#test").html(template(data));
	</script>
```

만약 같은 코드를 template을 쓰지 않았다면
```javascript
	var ss = "<h1>"+data.name+"</h1><h2>"+data.userid...
    $("#test").html(ss);
```
이런 식으로 작성을 했어야 할 것이다. (매우 간단한 예제라서 오히려 더 복잡해보이기도 한다..)

==template를 사용 할 경우 html코드를 따로 작성하고 data를 집어 넣는 방법인 반면==

==사용하지 않을 경우 html코드와 data를 넣는 것이 복잡하게 꼬여있다.==

단순한 코드인 경우엔 더 복잡해 보일 수 있지만 데이터가 10개 20개라면? 아마 "" ''만 20개씩 복잡하게 꼬여있는 불상사가 발생할 것이다.

또한 handlebars는 간단한 로직을 지원한다.

### handlebars의 로직 (반복문,제어문 등)

코드를 먼저 보도록 하자.
```javascript
	<script id="template" type="text/x-handlebars-template">
		{{#each .}}
		<h1>{{name}}</h1>
		<h2>{{userid}}</h2>
		{{/each}}
	</script>
	<script>
    	//만들어진 템플릿을 가져온다.
		var source = $("#template").html();
        
        //핸들바 템플릿을 컴파일.
		var template = Handlebars.compile(source);
        
        //실험용 데이터
		var data = [{name:"김세환",userid:"sehwan"},
					{name:"김연아",userid:"queen"},
					{name:"유병진",userid:"mj"},
					{name:"조던",userid:"jd"},
					{name:"송민기",userid:"babo"}
					];
		
        //템플릿과 데이터를 바인딩 후 html로 뿌려준다.
		$("#test").html(template(data));
	</script>
```
반복문의 시작은 {{#each .}}으로 끝은 {{/each}}로 하였다.

위와 같은 방법으로 JSON으로 받아온 데이터를 쉽게 HTML코드로 변환 할 수 있게 해주는 것이 Handlebars 이다.

```java
	public static void main(String[] args){
		System.out.println("hi");
	}
```