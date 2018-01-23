---
title: MITM 으로 인한 스트레스 줄이기
categories:
  - Tip
tags:
  - tip
  - MITM
  - disable certificate
date: 2018-01-16 10:16:21
thumbnail:
---
보안 때문이겠지만 회사에서는 https 패킷까지 감시하고 싶은가보다. 그를 위해 [`MITM`(Man-in-the-middle attack)](https://ko.wikipedia.org/wiki/%EC%A4%91%EA%B0%84%EC%9E%90_%EA%B3%B5%EA%B2%A9이라는 방법을 사용한다고 하는데, 기술적인 부분은 잘 모르겠지만 사용자 입장에서는 모든 https 패킷이 회사 인증서로 바뀌어서 돌아온다.

때문에 나같은 선량한 개발자가 개발을 위해 사용하는 apt-get npm pip 등 많은 package 매니저에서 오류를 내뿜는다. 
disable 하는건 보안상 취약하게 만드는 부분이지만, 개발을 하는 입장에서는 어쩔 수 없으므로 발견할때마다 disable 하는 방법을 남긴다.

### 인증서 등록
회사 인증서만 등록해도 많은 부분이 해결된다.
```bash
sudo cp ./{xxx}.crt /usr/local/share/ca-certificates/
sudo update-ca-certificate
```
### apt-get 

### curl
```bash
echo insecure >> ~/.curlrc
```

### npm
```bash
npm set strict-ssl false
```