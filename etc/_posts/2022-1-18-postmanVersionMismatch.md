---
layout: post
title: "[Postman] postman Version mismatch detected 포스트맨 구버전 실행"
categories: [etc]
comments: true
---

포스트맨 자동 업데이트를 켜놨었다.

어쩌다보니 최신버전으로 업데이트가 됐고. 갑자기 잘 먹히던 쿠키 싱크가 안되기 시작..

기능 자체가 조금 바뀐 것 같다. 이것저것 시도해보았지만 쿠키 싱크는 안먹히고. 허무하게 날려버린 오전 시간.

최신 버전은 가장 따끈한 버그 집합체라는 말이 떠오르고.. 다운그레이드를 마음먹고 구버전을 설치했다.

# Version mismatch detected

```
Looks like you`ve used a newer version of the Postman mapp on this system. Please download the lastest app ans try again.

```

안그래도 기능 찾다가 1시간을 소비했는데 구버전을 설치하니 나오는 에러.

최신 버전을 썼었던 것 같다. 최신 버전을 설치해서 실행하라는 문구. 당황했지만 당황하지 않은 척 다시 구글링.

똑같은 고민을 한 개발자가 절대 없을리 없다.

# 해결책

```
C:/user/{user}/AppData/Roaming/Postman
```
해당 경로를 삭제하고 구버전을 재설치 하면 정상적으로 실행이 된다.