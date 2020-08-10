---
layout: post
title: "[알고리즘]해커랭크 다시 시작"
categories: [algorithm]
comments: true
---

매일 똑같은 개발만 하다보니 지겹기도 하고

어느정도 개인적인 스킬업도 필요하다고 생각해서 [해커랭크](https://www.hackerrank.com)를 다시 시작했다.

분명히 예전에 가입했던 계정이 있었는데.. 까먹어서

그냥 새로 만들어서 시작!

이번엔 진짜 차근차근 하나하나 다 풀어볼 계획이므로

Problem Solving부터 시작.

Solve Me First

Compare the Triplets

A Very Big Sum

Diagonal Difference

풀이 완료.

(Diagonal Difference 푸는데 절대값을 안붙여서 3번이나 틀렸다.)

## 첫 번째 별을 땄다.

![1stStar](/assets/img/20200810/1.png)








회사에서 젠킨스를 배포 툴로 사용하고 있는데.

정말 뜬금없이 위의 에러가 발생했다.

Server returned HTTP response code: 501

호출 한 URL은 http://repo1.maven.org/ 

구글링을 해보니 1월 15일부로 http 통신을 중단한다고 한다.

maven을 통해 빌드하고 있으니 pom.xml에 repository URL만 바꾸면 되겠구나 생각했다.

변경 전
```XML
	<repository>
		<id>mvn2</id>
		<url>http://repo1.maven.org/maven2/</url> 
		<releases>
			<enabled>true</enabled>
		</releases>
		<snapshots>
			<enabled>true</enabled>
		</snapshots>
	</repository>
```

변경 후
```XML
	<repository>
		<id>mvn2</id>
		<url>https://repo1.maven.org/maven2/</url> 
		<releases>
			<enabled>true</enabled>
		</releases>
		<snapshots>
			<enabled>true</enabled>
		</snapshots>
	</repository>
```

하지만 똑같이 에러가 발생.. 분명 브라우저에서 들어가면 http는 501을 뱉고있고.

https로 접속하면 정상적으로 들어가지는데 무엇이 문제였을까..

## java 버전에 따른 protocol 에러.

한참을 삽질하다 발견한 java version에 따라 protocol_version이 다르다는 사실.

아래 표를 보면 java 6은 TLS 1.0이 기본. 

시스템에서 java 6 버전을 쓰고 있는데

*maven에서 요구하는 버전은 TLS1.2*여서 동일한 에러가 발생했었다.

| Java Version | SSL/TLS Default | Other Supported Versions |
|:--------|:--------:|--------:|
|Java 6	| TLS 1.0	| TLS 1.1 (update 111 and later), SSLv3.0*
|Java 7	| TLS 1.0	| TLS 1.2, TLS 1.1, SSLv3.0*
|Java 8	| TLS 1.2	| TLS 1.1, TLS 1.0, SSLv3.0*

따라서 TLS1.2v 으로 호출하도록 추가해주면 문제가 해결!

TLS1.2v 을 사용하게 하려면 아래의 코드를 추가하면 된다고 한다.

```
	-Dhttps.protocols=TLSv1.1,TLSv1.2
```

이클립스였으면 옵션 마지막 줄에 추가하면 되겠지만.. 젠킨스에서는 어디서 넣어야 하는가 찾는게 마지막 문제!



### MAVEN으로 빌드 할 경우

MAVEN 으로 빌드를 할 경우 JVM Option 에 위의 코드를 넣어주면 된다.

JVM Option은 숨겨져 있기 때문에 Builder 항목에서 고급을 누르면 표시된다.

![maven](/assets/img/20200302/1.JPG)



### ANT로 빌드 할 경우

이렇게 모든게 끝난 줄 알았는데..

MAVEN이 아니라 ANT로 빌드하는 시스템들이 있었다.

게다가 ANT 빌드의 경우 Jenkins 내에 JVM Option을 추가 할 수 있는 항목 또한 없었다..

결국 업체 문의 끝에 ANT Script 내에 JVM Option 추가하는 위치를 찾을 수 있었다.

빌드 할 시스템의 

exec 태그 안쪽에 arg value="-Dhttps.protocols=TLSv1.2"

를 추가 하면 된다. 



![ant](/assets/img/20200302/2.JPG)





