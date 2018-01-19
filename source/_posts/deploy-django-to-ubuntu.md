---
title: Utuntu에 Apache와 mod_wdg를 이용하여 django 배포
categories:
  - django
tags:
  - django
  - deploy
  - utuntu
  - apache
date: 2016-11-14 09:42:48
thumbnail:
---

## 소개
Django는 우리가 웹사이트를 구축하기 쉽도록 도와주는 파워풀한 웹 프레임워크이다. Django는 개발 시 사용할 수 있는 간단한 웹서버를 탑재하고 있어, 로컬에서 코드를 테스트해 볼 수 있다. 그러나 실제 운영 서버에서 사용하거나 더 향상된 보안을 위해서는 웹서버가 필요하다.

이번 가이드에서는 운영 서버 환경에 Django Virtual 환경을 어떻게 설치/구성하는지 살펴본다. Apache 서버를 이용해 프론트 애플리케이션을 핸들링하고 클라이언트의 요청을 Django app에 보내는 방법을 살펴볼 것이고, 이것은 `mod_wsgi`라는 Apache 모듈을 이용하여 Django와 Apache와의 커뮤니케이션이 가능하도록 설정할 것이다.

## 사전 지식 및 목표
이 가이드에서는 `Ubuntu 14.04` 서버를 이용할 것이고 `non-root` 유저를 사용하여 `sudo` 권한으로 설정할 것이다. Ubuntu 서버는 Azure나 AWS를 이용해 쉽게 생성 가능하다.
우리는 Django를 python 가상환경을 사용하여 설치할것이다. 가상환경은 우리의 프로젝트에 필요한 패키지를 격리된 환경에 설치 할 수 있도록 도와준다.

Django app을 Apache 인터페이스에 설정하기 위해 `mod_wsgi`라는 Apache 모듈이 필요하다. 

## Ubuntu repository를 이용한 패키지 설치
첫 단계로 우리는 필요한 것들을 Ubuntu repository를 이용해 다운로드하고 설치 할 것이다. Apache web Server, Apache를 Django App과 연결하기 위한`mod_wsgi` 모듈 그리고 우리 프로젝트에 포함된 python 패키지를 다운로드하기 위한 패키지 매니저인 `pip`가 필요하다.

우리는 Python3을 사용하므로 다음 명령어로 설치한다.

```bash
sudo apt-get update
sudo apt-get install python3-pip apache2 libapache2-mod-wsgi-py3
```
이 명령어로 Python3 pip, apache2, mod_wsgi 모듈까지 설치 할 수 있다.

## python 가상환경 설정
Ubuntu 리파지터리를 이용해 필요한 컴포넌트를 설치함으로써 Django 프로젝트를 시작 할 수 있는 준비가 되었다.
첫단계로 python 가상환경을 만들어보자. 이렇게 함으로써 우리 프로젝트가 다른 프로젝트나 설정에 영향 받지 않고 독립적인 실행이 가능하게 된다.

python 가상환경을 생성하기 위한 `virtualenv` 라는 커맨드를 `pip`이용하여 설치한다.

```bash
sudo pip3 install virtualenv
```

`virtualenv`를 설치했으면 우리 프로젝트를 git repository를 통해 clone 한다.

```bash
sudo apt-get install git
git clone https://github.com/xxxx/xxxx.git
```

clone 받은 프로젝트 디렉터리로 이동하여 가상환경을 만든다.

```bash
virtualenv myenv
``` 

`myenv`는 임의로 정한 가상환경의 이름이다. 이 명령어로인해 anchvy 디렉터리 안에 `myenv`라는 디렉터리가 생성된다. 이 디렉터리안에는 우리가 사용할 python과 pip가 설치된다. 우리는 이것을 이용하여 격리된 python 환경에서 우리 프로젝트를 실행 할 수있다.

그럼 python 가상환경으로 들어가 보자.
```bash
source myenv/bin/activate
```
가상환경으로 진입하면 쉘의 앞부분에 가상환경이름이 표시된다. 
```bash
(myenv) crynut84@anchovy-web:~/anchovy$
```

## python 패키지 설치
우리 프로젝트는 requirements.txt 파일에 python 패키지의 종속성이 표기 되어있다.
가상환경 내에서 이를 이용하여 패키지를 설치해 보자.
```bash
(myenv)$ pip install -r requirements.txt
```
pip는 `requirements.txt`에 기록된 패키지를 python 가상환경내에 설치 해 줄 것이다.
이를 확인하려면 myenv안의 폴더를 뒤져보면 된다.

## 프로젝트 설정 마무리
개발한 프로젝트를 운영환경(Production)에서 돌리려면 몇가지 설정을 해야한다. 디버깅을 위해 설정했던 기능을 꺼주어야하고, 프로젝트내에 포함된 스테틱 컨텐츠(css, jaavascript, image 등)를 한데모아 통합하는 일, 그리고 디비의 설정을 실제 디비로 바꿔주는 일등이 있다.

다음의 명령어로 프로젝트의 스테틱 파일을 한데 모을 수 있는데, 저 명령어를 수행하고 나면 settings.py의 STATIC_ROOT 경로로 파일이 모이게 된다.
왜 스테틱 파일을 모아야하는지는 다음 설명([Django static](http://crynut84.github.io/2016/11/14/django-static-file/))을 참조한다.
```bash
python manage.py collectstatic
``` 

프로젝트의 `settings.py`를 열어(vi XXX/settings.py) DB설정을 실서버로 변경한다. (이부분은 향후 자동화하는 방법이 필요하다.)

```javascript
DATABASES = {
        'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '{DB_NAME}',
                'USER': '{DB_USERNAME}',
                'PASSWORD': '{DB_PASSWORD}',
                'HOST': '{DB_HOST_ADDRESS}',
                'PORT': '3306'
        }
}
```

그리고 pip를 통해 mysql에 접속하는 python 모듈인 `mysqlclient`를 설치한다.

```bash
pip install mysqlclient
```

>이 경우 Azure Ubuntu에서 `mysql_config: not found` 오류가 발생한다
>sudo apt-get install libmysqlclient-dev을 먼저 설치 후 다시 mysqlclient를 설치하면 된다.


DEBUG 모드가 아니므로 DEBUG 속성을 False로 수정하고, 모든 클라이언트 요청을 처리하기위해 `settings.py`의 `ALLOWED_HOSTS`  속성을  수정한다.

```bash
DEBUG = False
ALLOWED_HOSTS = ['*']
```

### Apache 설정
우리 Django 프로젝트를 Apache와 연결시키는 방법을 알아보자. Apache는 클라이언트의 요청을 WSGI 포맷으로 변경하여 Django 어플리케이션으로 전달해야하는데 이 작업을 `mod_wsgi` 모듈이 담당한다. 이 설정은 아까 설치할때 이미 활성화되어있다.
위 경로의 apache 설정을 수정한다.

```bash
sudo vi /etc/apache2/sites-available/000-default.conf
```
![image](https://cloud.githubusercontent.com/assets/5028400/20252478/f2178cda-aa65-11e6-9c2d-53f904fefe96.png)

우선 static file의 경로 설정을 한다. 그 다음 wsgi 경로 설정. 그 후 python 경로설정과 virtual 환경 설정을 한다.

## ref.
https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-apache-and-mod_wsgi-on-ubuntu-14-04
