---
title: Ubuntu 16.04에  elasticsearch v5.0 설치하기
categories:
  - elasticsearch
tags:
  - elasticsearch
  - install
date: 2016-11-03 09:24:05
thumbnail:
---

## elasticsearch v5.0 설치 가이드
elasticsearch는  `lucene` 기반의 실시간 검색을 제공하는 분산 검색엔진이다. 검색 기능뿐만 아니라 강력한 집계 기능을 제공해 실시간 분석 엔진으로도 활용 가능하며, 데이터를 저장할 수 있어 NoSQL 저장소로도 활용할 수 있다. 넷플릭스(Netflix), 깃허브(Github), 위키피디아(Wikipedia) 등 잘 알려진 사이트에서도 검색, 로그, 분석 등 다양한 용도로 클러스터를 운영하고 있다.

이 문서는 elasticsearch v5.0을 Azure 가상머신(Ubuntu 16.04 LTS) 버전에 설치하기 위한 가이드이다.
> 설치 정보는 [elasticsearch document](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html) site를 참고함.

## 사전준비

### Java(JVM)
elasticsearch는  Java로 구현되어있으며, 구동을 위해서는 [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html)이 필요한데 Oracle's Java와 openJDK만을 지원한다. 또한 elasticsearch를 분산서버에 구축할 경우 **모든 node는 같은 버전의 Java**를 사용해야한다.

Java 버전은 *1.8.0_73 또는 그 이상*을 권장한다. 다른 버전의 자바를 설치 할 경우 구동이 안될 수 있다. elasticsearch는 `JAVA_HOME` 환경변수에 지정된 Java version을 사용한다.

#### Ubuntu에 Java 8  설치
`apt-get`을 사용한 설치방법이 가장 쉽다. apt-get은 package의 repository에서 package를 검색/설치할 수 있는 패키지매니저이다.
java8은 azure ubuntu의 기본저장된 reposigory에서는 설치할 수 없으므로 `webupd8team`의 Java PPA repository를  먼저 추가 후 설치한다.

```
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java8-installer
```
#### Java 설치 검증
java 설치가 완료되면`java -version` 명령어로 Java가 정상적으로 설치 되었는지 elasticsearch에서 요구하는 `1.8.0_73`이상의 버전이 설치되었는지 확인합한다. (2016년 11월 3일 기준 `1.8.0.111` 버전이 설치됨)

```bash
$ java -version
java version "1.8.0_111"
Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)
```

## Debian package를 이용한 elasticsearch 설치
우리는 Debian 계열의 리눅스인 Ubuntu를 사용하므로 Debian package를 이용하여 설치한다.
패키지는 elasticsearch 사이트에서 [다운로드](https://www.elastic.co/downloads/elasticsearch)받거나 APT repository를 이용해 설치 할 수 있다.

### PGP Key 다운로드
elasticsearch는 PGP Key를 이용하여 패키지를 암호화해 놓았으므로 아래의 방법으로 `public signing key`를 다운로드 후 설치한다.
```bash
$ wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
### APT Repository를 이용하여 설치
APT를 이용하여 설치하려면 `apt-transport-https`라는 패키지가 필요한데 설치하지 않은 경우 아래와 같이 설치한다.
```bash
$ sudo apt-get install apt-transport-https
```
repository의 정보를 `/etc/apt/sources.list.d/elastic-5.x.list` 파일에 기록한다.
```bash
$ echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```
이제 아래 명령어를 이용해 elasticsearch를 설치한다.
```bash
$ sudo apt-get update && sudo apt-get install elasticsearch
```

## 시작프로그램으로 등록
elasticsearch의 시작과 중지는 시스템에 따라 SysV `init`이나 `sysemd`를 통해 관리된다. 자신의 시스템이 어떤 것을 사용하는지 보려면 아래 명령어로 확인 가능합니다.
```bash
$ ps -p 1
  PID TTY          TIME CMD
    1 ?        00:00:08 systemd
```
Ubuntu 16.04 LTS의 경우 `systemd`를 사용하는것을 알 수 있다.

### `systemd` 를 사용한 elasticsearch 시작프로그램 등록
시스템 시작 시 elasticsearch가 시작될 수 있도록 하려면 아래의 명령어를 실행한다.
```bash
$ sudo /bin/systemctl daemon-reload
$ sudo /bin/systemctl enable elasticsearch.service
```
아래 명령어를 이용해 수동으로 켜고 끌 수 있다.
```bash
$ sudo systemctl start elasticsearch.service
$ sudo systemctl stop elasticsearch.service
```
위의 명령어는 elsaticsearch가 잘 구동되었는지 혹은 잘 멈추었는지에 대한 정보를 화면에 표시하지 않는다. 
정보를 보려면 로그 파일을 확인하면 된다.

> 로그 파일 경로 : /var/log/elasticsearch/

### `init` 를 사용한 elasticsearch 시작프로그램 등록

```bash
$ sudo update-rc.d elasticsearch defaults 95 10
```
아래 명령어를 이용해 수동으로 켜고 끌 수 있다.
```
$ sudo -i service elasticsearch start
$ sudo -i service elasticsearch stop
```

## 잘 실행 되었는지 확인
elasticsearch node가 실행 중인지 HTTP 요청으로 알아볼 수 있다. (default port: 9200)
```bash
$ curl localhost:9200
{
  "name" : "zBrsnqs",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "27nbRlqPSJuLLtnKCzB7wQ",
  "version" : {
    "number" : "5.0.0",
    "build_hash" : "253032b",
    "build_date" : "2016-10-26T05:11:34.737Z",
    "build_snapshot" : false,
    "lucene_version" : "6.2.0"
  },
  "tagline" : "You Know, for Search"
}
```

## 환경설정
elasticsearch 기본 설정 파일은 `/etc/elasticsearch/elasticsearch.yml`에 위치하는데  설정에 대한 자세한 설명은 [여기](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)를 참조하면 된다.

Debian package의 경우 시스템 환경설정 파일(`/etc/default/elasticsearch')도 존재한다.
![image](https://cloud.githubusercontent.com/assets/5028400/19964440/042d7f4c-a205-11e6-99df-d5203c260028.png)

## Debian package의 디렉터리 구조
데비안 패키지의 설정, 로그, 데이터 디렉터리에 대한 설명이다.
![image](https://cloud.githubusercontent.com/assets/5028400/19964485/4514547c-a205-11e6-84e5-387cca035bba.png)

> Production server에는 다음 설정이 꼭 들어가야함.
>[Important System Configuration](https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html#dev-vs--prod)


## JVM 메모리 설정
elasticsearch v5.0부터 메모리 설정이 `/etc/init.d/elasticsearch`에서  `/etc/elasticsaerch/jvm.options`로 옮겨졌다.
```
참고: For the current version of elastic (5.0) you can configure jvm 
startup options in the jvm.options file usually located under directory
/etc/elasticsearch/jvm.options:

-Xms1g -Xmx1g
```