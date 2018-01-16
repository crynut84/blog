---
title: Visual Studio T4 Template 오류
categories:
- .NET
tags:
- T4
- .NET 
- C#
- VisualStudio
date: 2015-06-23 09:02:13
thumbnail:
---

### Visual Studio T4 Template 오류
Visual Studio에서 제공하는 T4는 `Text Template Transformation Toolkit`의 약자인 TTTT를 줄인 말이다. 우리 회사에서는 [Visual Studio T4 Template](https://msdn.microsoft.com/ko-kr/library/bb126445.aspx)을 프레임워크 전반에 활용하고 있다.

예를들어 특정 폴더 밑에 있는 jsvascript나 css를 한대 묶어 [Bundle](http://www.asp.net/mvc/overview/performance/bundling-and-minification)을 코드를 자동 생성하기도 하고, 정해진 Generic type의 Interface만 선언하면 구현 class를 생성해주기도 한다.

어느날 T4 파일에서 아래와 같은 오류가 나는데 도무지 해결 방법을 찾을 수 없었다.
![T4 error message](https://crynut84.github.io/images/posts/t4-must-end-with-a-class-feature-error/t4-error.jpg)
>A template containing a class feature must end with a class feature.

오류 내용만 봐서는 마지막에 구문 오류가 있는지 아무리 살펴보아도 그런것은 찾을 수 없었다.
아래는 문제가 되는 코드의 마지막 부분 캡처이다.
![T4 code](https://crynut84.github.io/images/posts/t4-must-end-with-a-class-feature-error/t4-code.jpg)

##### A template containing a class feature must end with a class feature 오류 해결 방법
결국 삽질 끝에 이 오류는 마지막에 보이지 않는 스페이스(공백)이 들어 있어서였다. 허무하다. 오류 내용이 친절하지 않지만 큰 의미에서 틀리지도 않았다.
![T4 code](https://crynut84.github.io/images/posts/t4-must-end-with-a-class-feature-error/t4-solution.jpg)
위의 오류가 난다면 **마지막의 공백을 제거**하면 된다.
