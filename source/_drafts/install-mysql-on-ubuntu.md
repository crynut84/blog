---
title: Ubuntu 16.04에 MySQL Server 설치하기
categories:
  - null
tags:
  - MySQL
date: 2018-01-17 16:56:52
thumbnail:
---

`Ubuntu 16.04` 에 MySQL Server 설치방법

### 설치
```bash
sudo apt-get update
sudo apt-get install mysql-server -y
```

Tip. 설치 중간에 나오는 `root` 계정에 대한 비밀번호도 자동으로 입력하고 싶다면 다음 명령어를 먼저 수행 후 설치한다.
```bash
sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password password {비번}'
sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password {비번}'
```
### 실행 & 접속
```bash
service mysql start # 실행
mysql -u root -p # 접속
```

### 원격 접속 허용
MySQL 초기 설정이 로컬 접속만 허용되어있기 때문에 리모트 접속이 불가하다.

### Related Posts
