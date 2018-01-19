---
title: django static에 대해 알아보자
categories:
  - django
tags:
  - django
  - static
date: 2016-11-14 09:05:35
thumbnail:
---

우리가 개발한 프로젝트를 운영 서버로 배포하다 보면 static 파일을 한데 모으는 `python manage.py collectstatic`이란 명령어를 접하게된다.
`collectstatic` 명령어는 Django 프로젝트의 여러 app에서 사용하는 스테틱 파일을 한 곳(경로)으로 모아주는 역할을 한다.(물리적으로 파일을 copy 한다는 뜻이다)

## 웹 서버와 웹 어플리케이션

Django를 이용하여 웹 애플리케이션 개발할 때 Django에 내장된 경량의 웹서버를 사용하게 된다. 하지만 [공식문서](https://docs.djangoproject.com/en/1.10/intro/tutorial01/#the-development-server)에도 경고하고 있는것처럼 이것은 실제 웹서버가 아니므로 Production 환경에서는 사용해서는 안 된다.

>don’t use this server in anything resembling a production environment. It’s intended only for use while developing. (We’re in the business of making Web frameworks, not Web servers.)
>
>프로덕션 환경과 유사한 상황에서는 사용하면 안 된다. 오직 개발 중에만 사용해라.(이건 웹 프레임워크를 만들기 위한 것이지 웹서버가 아니다.)

그렇다! `runserver`로 사용하는 개발용 서버는 서버가 아니다. 실제 Production 환경에서는 [Apache](https://httpd.apache.org/)나 [Nginx](http://nginx.org/)와 같은 웹서버를 사용해야 한다. 즉 사용자의 요청(Request)이 들어오면 웹서버가 받아서 적절한 처리 후에 웹 애플리케이션에 넘겨주고 웹 애플리케이션은 우리가 작성한 로직에 따라 적절한 처리 후에 웹서버에 돌려주어 사용자에게 응답(Response)하는 것이다.

![image](https://cloud.githubusercontent.com/assets/5028400/20266416/5b31d844-aab9-11e6-90fc-d9cd73047ef7.png)

이것을 간단하게 도식화하면 위의 그림과 같은데 웹서버와 웹 애플리케이션 사이에는 WSGI(Web Server Gateway Interface)라는 규약으로 통신하게 된다.

## 스테틱 파일을 모으는 이유?

HTTP 프로토콜을 이용하여 단순히 파일을 응답하는 처리는 웹서버가 잘하는 일이다. 사용자의 요청의 URL을 해석하여 서버 내의 물리적인 위치의 파일을 찾아 HTTP 응답으로 돌려주는 단순한 작업이기 때문이다. 그에 반해 웹 애플리케이션 동적인 데이터를 처리하기 위해 만들어졌다. 예를 들어 로그인한 사용자마다 카트의 내용이 동적으로 변하여 다르게 보인다든가 하는 처리를 말하는 것이다.

CSS, Javascript, image 파일들로 이루어진 static 파일을 웹어플리케이션인 Django가 처리하기에는 너무 비효율적이다. 웹서버에서 웹 애플리케이션으로 요청을 전달하는 데는 WSGI라는 변환과정을 거치며 이것은 자원을 소모하는 일이고, 굳이 웹서버도 할 수 있는 일을 웹 애플리케이션에서 할 필요가 없는 것이다.

그래서 Django에서는 Production에서의 static 파일을 처리하는 기능을 담고있지 않다.

## 개발할 때 보니까 되던데?

Django 개발할 때는 static 파일이 잘 전송되는 것을 볼 수 있다. 이것은 `INSTALLED_APPS`의 django.contrib.staticfiles이라는 모듈이 담당하고 있으며, 그마저도 settings.py의 `DEBUG` 속성을 False로 바꾸면 동작하지 않게된다.

이때부터는 static 파일의 처리는 웹서버가 담당하게 되는 것이다. [아파치 설정](https://github.com/house142/anchovy/wiki/%EC%9A%B0%EB%B6%84%ED%88%AC%EC%97%90-Apache%EC%99%80-mod_wsg%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-Django-%EC%84%9C%EB%B2%84-%EA%B5%AC%EC%B6%95#apache-%EC%84%A4%EC%A0%95) 중에도 static 파일의 경로를 정의하는 설정이 포함되는 것을 알 수 있다.


## 스테틱관련 설정
Django [공식 문서](https://docs.djangoproject.com/en/1.10/ref/settings/#std:setting-STATIC_ROOT)를 보면 settings.py에 정의 할 수 있는 static 관련 설정이 나온다.
 - `STATIC_ROOT`
 - `STATIC_URL`
 - `STATICFILES_DIRS`

### STATIC_ROOT
실 서버 배포 시 static 파일을 `collectstatic` 하기위한 절대 경로이다. 기본 값은 `None`이며 다음과 같이 설정할 수 있다.

```python
STATIC_ROOT = "/var/www/example.com/static/"
```

staticfiles contrib app이 활성화 되었을 때(기본적으로 Django 프로젝트를 생성하면 `INSTALLED_APPS`에 포함) 'collectstatic' 콘솔 명령어를 수행하면 해당 디렉터리로 static 파일들이 모인다.

### STATIC_URL
URL로써 static 파일의 위치를 가르킬 때 사용하는 위치이다. 마찬가지로 기본값은 `None`이며 아래와 같이 설정한다.
```python
STATIC_URL = "/static/" or "http://static.example.com/"
```

주의 할 점은 마지막에 `/`를 꼭 붙여주어야 하고 `/`만 써서 root 경로를 가르키게 하면 안된다.

### STATICFILES_DIRS
앱에 포함된 static 파일의 위치를 추가할 수 있다. 기본값은 `[]`(비어있는 리스트) 이고, 아래와 같이 설정한다.
```python
STATICFILES_DIRS = [
    "/home/special.polls.com/polls/static",
    "/home/polls.com/polls/static",
    "/opt/webfiles/common",
]
```
앱에 포함된 기본 static 파일의 경로는 {APP_NAME}/static이다. manage.py의 커맨드 명령어인 `findstatic`을 수행하면 우리가 찾고자 하는 static 파일의 풀 경로를 보여준다.
이때 우선순위는 기본 앱의 경로보다 `STATICFILES_DIRS`에 명시된 경로가 먼저 사용된다.

### Prefixes
STATICFILES_DIRS 지정 시 Optional 속성으로 Prefix를 넣을 수 있다.
```python
STATICFILES_DIRS = [
    # ...
    ("downloads", "/opt/webfiles/stats"),
]
```
예를 들어위처럼 `downlaods`라는 prefix를 넣으면 어떻게 될까? 해당경로에 있는 파일들은 `collectstatic` 명령을 내렸을때 `STATIC_ROOT`에서 지정한 폴더 하위에 `downloads`라는 폴더를 만들어 모이게 된다.
당연하게도 실제 제공되는 URL도 `STATIC_ROOT`에서 지정한 경로에 `downloads`를 붙여 제공하게 된다.


### ref.
- http://blog.hannal.com/2015/04/start_with_django_webframework_06/
- http://tuwlab.com/ece/26571
- https://docs.djangoproject.com/en/1.10/ref/contrib/staticfiles/#module-django.contrib.staticfiles
- https://docs.djangoproject.com/en/1.10/howto/static-files/deployment/
- https://docs.djangoproject.com/en/1.10/howto/static-files/
