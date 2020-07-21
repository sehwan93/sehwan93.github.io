---
layout: post
title: "[Android/웹뷰] PWA를 도입한 안드로이드 웹뷰에서 net:ERR_FAILED 발생하는 이슈"
categories: [android]
comments: true
---

내가 만든 어플리케이션은 PWA를 도입한 후 네이티브 웹뷰를 씌웠었다.

PWA의 도입으로 유저들이 조금은 편해질 줄 알았는데 거의 활용하는 모습을 보지 못했고..

유저들이 친숙한 네이티브 앱 형태로 만들기 위해 웹뷰를 띄우고 푸쉬와 SMS 등등 몇가지 네이티브 기능만 도입하였다.

그러던 중 간헐적으로 뒤로가기 시 net:ERR_FAILED 오류가 발생하여 화면이 뜨지 않는다는 문의가 들어왔다.


![errfailed](/assets/img/20200501/errfailed.JPG)

확인해보니 service Worker에서 fetch를 하는 도중 오류가 나는 것으로 파악하였다.

# 네이티브 앱에선 Service Worker를 사용하지 않도록 하자
 
구글링을 열심히 해보니 이미 알려진 크롬 이슈인 듯 하다. 

https://github.com/react-native-community/react-native-webview/issues/671

https://bugs.chromium.org/p/chromium/issues/detail?id=977784

해결책은 웹뷰일 경우 서비스워커를 등록 해제하는 코드가 나와있었다.

```javascript
	navigator.serviceWorker.getRegistrations().then(function(registrations) {
	   for (let registration of registrations) {
	       registration.unregister();
	   }
	});
```

해당 코드를 커밋 후 테스트 해본 결과 로컬과 개발서버에선 정상적으로 작동하는데.

이상하게 운영 환경에선 여전히 똑같은 에러가 발생..


## 결국 서비스 워커의 fetch 기능을 삭제해버렸다.

PWA를 도입하였으나 안드로이드 사용자들은 대부분 네이티브 앱을 설치하여 사용하고.

ios에선 웹을 통한 사용을 권장해왔으니.

과감하게 service Worker의 fetch 기능을 삭제해버렸다. 

serviceWorker.js 의 fetch 관련된 소스를 전부 주석처리.

추후 시간이 나면 해결책을 조금 더 찾아봐야겠다.




