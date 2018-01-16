---
title: node.js 소개 및 설치
categories:
- node
tags:
- node
date: 2015-06-03 08:13:47
thumbnail: nodejs_logo_green.jpg
---

벌써 개발자로 살아온지 만 5년이 넘었다. 두번째 회사에 다니고 있지만, 두 회사 모두 닷넷 기술을 사용하여서, 닷넷은 나름 빠삭(?)하다 생각하지만 Java, Python, GO lang등 다른 언어나 기술 환경은 잘 모르는 처지가 되었다. 

요즘 [폴리글랏](http://en.wikipedia.org/wiki/Polyglot_(computing) 프로그래머가 유행이라던데, 개인적으로 만드려고 했던 서비스를 만들면서, 닷넷을 벗어나 새로운 언어와 환경에서 만들기로 하고, 평소 관심을 가지고 있었던 node.js를 공부해 보기로 하였다.

## node.js 개요 ##
node.js는 서버사이드에서 동작하는 javascript이다. 브라우저에서 주로 사용되는 javascript가 어떻게 서버사이드에서 동작하는 것일까? 답은 바로 [Google V8](http://ko.wikipedia.org/wiki/V8_(%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EC%97%94%EC%A7%84))(자바스크립트 엔진) 덕분이다. V8은 javascript를 JIT 컴파일하여 빠른 속도로 실행시켜주는 엔진이고, 크롬에서도 쓰이고 있으며, 오픈소스로 공개함으로써 브라우저의 전유물이었던 javascript를 외부에서 사용 할 수 있는 길을 열었다. 
node.js를 만든 Ryan Dahl(라이언달)도 반응성이 뛰어난 애플리케이션을 만들려고 하였을 때, V8을 써서 javascript의 비동기(async)를 이용한 node를 개발 할 수 있었다. 

[JSconf EU 2009](http://holgerblank.com/2009/speaker/speakers_selected.html) 행사에서 라이언달(Ryan Dahl)이라는 개발자가 Node.js, Evented I/O for V8 Javascript라는 세션을 발표한다.
이 발표에서 라이언달은 node.js를 javascript를 이용해 I/O 프로그래밍을 할 수 있고, 개발에 있어 성능을 높이는데 집중했다고 한다.

## node.js 설치
ubuntu 14.04 LTS(64bit)에 node.js 설치해보자.

### apt-get(Advanced Packaging Tool)을 이용하여 설치
apt-get을 통해 설치하는것이 여러모로 편리하지만 한가지 주의 할 사항이 있다. 기본 repository에 있는 node.js 버전이 최신버전이 아닐 수 있다는 점이다. 
오늘(2015-06-02) 기준으로 node.js 사이트에서 확인한 버전은 v0.12.4 버전이다. 그런 ubuntu 기본 repository에는 어떤 버전의 node.js가 있는지 확인 해 보자

```bash
sudo apt-get udpate    
sudo apt-cache policy nodejs
```

위의 명령을 통해 확인해 보면 다음과 같이 v0.10.25버전이 들어있는 것을 알 수 있다.
![](http://i.imgur.com/RGcHs3R.png)

그럼 최신버전의 node.js를 apt-get을 통해 설치하려면 어떻게 해야할까?

기본적으로 apt-get을 사용하면 ubuntu에서 공식 운영하는 repository를 사용하여 다운로드 받게 된다. PPA(personal package archive)는 사설로 운영하는 저장소인데 node.js의 최신버전은 NodeSource라는 곳에서 관리한다.  

다음과 같이 node.js의 PPA를 추가한다.

```bash
curl -sL https://deb.nodesource.com/setup_dev | sudo bash -
```

그리고 다시 apt package의 node 버전을 확인해 보면 다음과 같이 v0.12.4 인것을 확인 할 수 있다. 최신버전을 설치하기 위한 사전작업은 끝났다.  apt-get을 사용하여 설치해 보자.
![](http://i.imgur.com/eImMCUK.png)

```bash
sudo apt-get install nodejs
```

이렇게 설치하면 node.js와 npm(node package manager)가 함께 설치된다.
설치를 마쳤다면 node -v이라고 입력하여 설치된 버전을 확인 할 수 있다.

ps. node.js는 여러버전을 중복하여 설치 할 수 있는데 NVM (node version manager)을 이용하면 좀 더 쉽게 설치 및 버전 관리가 가능하다.(시간이 된다면 소개하겠다).
