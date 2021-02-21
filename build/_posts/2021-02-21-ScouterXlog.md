---
layout: post
title: "[Scouter]스카우터 Xlog에 찍히는 점 최소 응답 시간 설정"
tags: [Scouter]
categories: [build]
comments: true
---

Apcahe + Tomcat + Java + Scouter 사용 중.

오픈소스 모니터링 APM 툴로 사용중인 Scouter.

도입 후 너무 요청은 나오지 않아 불편함이 있었는데

최근 도입된 시스템을 보니 모든 요청이 다 표시되서 확인해보니 시스템마다 XLog에 표시 할 최소 응답 시간을 설정해줄 수 있었다. ( 모든 시스템이 일괄로 적용해야 하는줄 알았는데 개별 적용이 가능! )


# 방법

해당 시스템 우클릭 후 Configure에 들어가서

xlog_lower_bound_time_ms 설정값을 바꿔주면 끝.

설정값 자체를 삭제하면 모든 요청을 보여주고

100을 설정하면 0.1초 이상 걸린 요청 건들만 보여준다.

![Scouter1](/assets/img/20210221/1.JPG)

![Scouter2](/assets/img/20210221/2.JPG)











