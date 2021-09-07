---
layout: post
title: "[리눅스].bash_profile의 용도,위치와 적용방법(source)"
tags: [리눅스]
categories: [build]
comments: true
---

##.bash_profile 용도는?
 윈도우든 리눅스든 CLI를 하다보면 어디서든 실행이 되는 명령어들이 있고, 해당 파일이 있는 곳에서만 실행 되는 명령어들이 있다.
 
 개발자라면 늘 접하게 되는 예로는 java를 설치 후 CMD 창에서 java -version을 쳐도 버전이 나오지 않지만. 환경변수에 추가하고 나면 java -version을 어디에서나 쳐도 버전이 출력된다.

이렇듯 리눅스에서 환경변수와 같은 역할을 하는 파일이 .bash_profile
( 비슷한 용도로 /etc/profile /etc/bashrc 가 있다. .bash_profile 보다 적용되는 범위가 크다 )

## .bash_profile의 위치

위에서 언급한 profile,bashrc와 다르게 bash_profile은 해당 유저에게만 적용된다.

고로 일반적인 경우 위치는 당연히 해당 유저의 홈 디렉토리에 위치한다.

```aidl
//홈 디렉터리 이동
cd ~

//.bash_profile파일 열기
vi .bash_profile
```

## .bash_profile 적용이 안됩니다!

.bash_profile에 패스를 등록했는데 여전히 명령어가 먹히지 않을 경우가 있다.

위와 같은 파일들은 bash가 실행 될 때 불러오기 때문에 수정한 파일은 적용이 되지 않은 것.
이런 경우에는 source 명령어를 통해 바로 적용 시킬 수 있다.
```aidl
//bash_profile 파일 적용
source ~/.bash_profile
```


