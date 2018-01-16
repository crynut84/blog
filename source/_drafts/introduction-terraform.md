---
title: Terraform으로 AWS 인프라 관리하기
categories:
- aws

tags:
  - terraform
  - aws
  - cloud
  - infra
  - vpc
date: 2018-01-11 07:38:45
thumbnail:
---
![Terraform logo](https://user-images.githubusercontent.com/5028400/34799823-a839efb8-f6a4-11e7-8cf2-ff27f8ef30f3.png)

불과 몇년 전만해도 클라우드 서비스가 낯설게 느껴졌는데 이제 `AWS`나 `Azure`와 같은 클라우드 서비스가 없다면 어떻게 개발하나 싶다.

`AWS`를 사용하면 손쉽게 인프라를 구축 할 수 있는데 아마 처음 접하는 사람들이 가장 많이 쓰는 방법은 `AWS Management Console`을 통해 클릭하는 방법일 것이다. 

비교적 작은 규모의 회사 인프라도 `AWS`위에 인프라를 구축하다보면 이것저것 많이 생기게 되고, 각종 rule들은 왜 정의 했는지 알 길이 없다. 

드물지만 다른 region에 동일한 환경을 구축해야 한다던지하는 요구사항이 생기면 난감하다. 똑같이 보고 구축한다고해도 사람이라는게 실수를 하고 각종 rule중에 빼먹는게 생기기 마련이다.

언젠가 python의 aws sdk인 boto를 이용해서 회사 인프라를 구축하고 명령 한번으로 동일한 환경을 구축했다는 세미나를 본적이 있는데 저렇게 한땀한땀  나름 방법이다. 중요한 것은 기록이 남는다는 것이니..

[Terraform](https://www.terraform.io/intro/index.html)은 인프라를 구축하고, 변경 및 버전화 할 수 있는 도구이다. 즉 코드로 인프라를 관리 할 수 있다. 코드로 관리하니 `git`같은 도구로 버전관리 할 수 있고, 주석도 쓸 수 있고, 변경 시 왜 변경되었는지 커밋 메세지도 볼 수 있다.

프로비저닝 시에도 사람이 하는게 아니니 인프라 코드만 잘 작성 해 놓았다면 실수 할 여지가 없다.

AWS의 `CloudFormation`과 비슷한 역할을 하는데 회사에서 다른분이 Terraform을 소개해 주셨다.(우리팀에서는 이 기술을 쓸 예정) 나도 하나의 기술을 배워서 여러군데 써먹는게 좋으니 여러가지 인프라를 관리하는 `Terraform`이 더 매력적으로 보였고 제공하는 [document](https://www.terraform.io/docs/index.html)도 훌륭해 보인다.


## 설치
Terraform은 [다운로드](https://www.terraform.io/downloads.html) 페이지에서 단일 바이너리 형태로 배포한다.

자신의 OS에 맞는 바이너리를 다운받아 압축을 풀고 Path 설정을 해주면 설치는 완료된다.

아래는 `Ubuntu`의 예이다.

```bash
$ curl -O https://releases.hashicorp.com/terraform/0.11.2/terraform_0.11.2_linux_amd64.zip
$ unzip terraform_0.11.2_linux_amd64.zip
$ sudo mv terraform /usr/bin
$ terraform -v
Terraform v0.11.2
```

## 기본 사용법


