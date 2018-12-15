---
layout: post
title: "2018-12-16-젠킨스 Launch slave agent via Java Web Start"
img: 20181216/jenkins.png
date: 2018-12-016 00:43:00 +0300
description: 젠킨스 노드 관리에서 Launch slave agent via Java Web Start 옵션이 안보일 때. 
tag: [젠킨스]
category: IT
---

# 젠킨스 노드 관리에서 Launch slave agent via Java Web Start 옵션이 안보일 때. 

![정상화면]({{site.url}}/assets/img/20181216/정상화면.png)

 젠킨스를 공부하려고 관련 책을 회사에서 사줘서 읽고있는데. 시작부터 문제가 생겼었다.
 
 책에서는 표시되어있는 옵션이 내 노트북에 설치한 젠킨스에는 나오지 않았다.
 
 중요하다고 한 슬레이브 노드 실행 방식에서 또 제일 중요하다고 한 Launch slave agent via Java Web Start 옵션이 보이지 않는 것이였다.
 
 구글링 해보니 JNLP 사용을 위한 TCP 포트를 열어주어야 표시가 된다고 한다.
 
 한글 버전 기준으로 Jenkins 관리 > Configure Global Security > Agents 탭에 TCP port for JNLP agents 옵션을 바꿔주면 된다.
 
![agents]({{site.url}}/assets/img/20181216/agents.png)
 
 <iframe src="https://coupa.ng/bgd8RK" width="120" height="240" frameborder="0" scrolling="no"></iframe>
 <iframe src="https://coupa.ng/bgd803" width="120" height="240" frameborder="0" scrolling="no"></iframe>
 <iframe src="https://coupa.ng/bgd81d" width="120" height="240" frameborder="0" scrolling="no"></iframe>
 <iframe src="https://coupa.ng/bgd81x" width="120" height="240" frameborder="0" scrolling="no"></iframe>