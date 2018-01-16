---
title: git 간단 개념과 설치 방법
categories:
- git
tags:
- git
date: 2015-06-16 08:46:53
thumbnail:
---

이전 포스팅에서 [분산 버전 관리 시스템의 개념](https://crynut84.github.io/2015-06-10/introduction-to-git.html)을 살펴보았으니, 대표적인  분산 버전 관리 시스템인 git에 대해 알아보려 한다.

git [공식 사이트](http://git-scm.com) 메인페이지에 보면 git을 아래와 같이 소개한다.

> ***Git is easy to learn*** and has a tiny footprint with lightning fast performance. It outclasses SCM tools like Subversion, CVS, Perforce, and ClearCase with features like cheap local branching, convenient staging areas, and multiple workflows.

배우기 쉽다는 것인데, 내가 처음 git을 배울 때는 다음과 같은 이유로 git이 무지 어려웠다.

- .NET을 주 언어로 사용하는 개발자로 윈도우의 GUI 환경에 익숙하다.
	- git GUI 환경이 있기는 하지만 결국 CLI을 알아야 한다.
- 기존에 사용하던 버전 관리 시스템(TFS, SVN등)의 개념으로 이해하려 했다.
	- git을 배우려면 기존의 생각을 버리는게 더 빠르다.
- git과 [github](https://github.com/)의 개념을 혼동.
	- github는 git 기반의 온라인 소스 관리 서비스이다.
	- 많은 자료에서 git과 github를 함께 설명하다 보니 해깔렸다.

그래서 나는 순수(?) git을 기능을 먼저 알아보려고 한다.

## git 소개
git은 [공식 사이트](http://git-scm.com)에 document가 굉장히 잘 만들어져 있다. 요즘 오픈소스는 대부분 document에 공을 들이는 것 같다. 고마운 분들이 번역해 놓은 [한글판](http://git-scm.com/book/ko/v2)도 있다. 덕분에 특별히 책을 구입하지 않아도 배울 수 있다.

[About 페이지](http://git-scm.com/about)에가보면 다음의 6가지 주제로 git을 소개하고 있다. 이 개념들이 기존 버전관리 시스템과 다른점이고 git을 사용하면서 얻을 수 있는 이점이다.

- Branching and Merging
- Small and Fast
- Distributed
- Data Assurance
- Staging Area
- Free and Open Source

### Branching and Merging
git은 branch를 만드는데 부담이 없다. 그렇기 때문에 작은 단위의 기능으로 새 branch를 만들고 거기에 원하는 기능을 개발하거나, 실험하고 싶었던 기능을 실험 할 수 있다.  실험에 실패한다면? 그냥 branch를 버리는것으로 끝난다. 아무런 사이드 이펙트가 없다. 나는 TFS를 이용할 때 Branch를 만들때마다 부담스러웠다. 만드는 속도, 용량등 자주 만들기에는 너무 큰 부담이었고(덕분에 회사에 품의를 올려 256G SSD를 추가 구매 할 수 있었지만..) git을 사용하면 이런 고민을 조금은 덜 수 있다.

git은 branch를 만들어 구현하고, 구현한것을 commit 하고, 때때로 원래 branch로 돌아가 심각한 버그에대한 hotpatch를 만들 수 있다.

### Small and Fast
branch를 만들고 merge하는 것이 부담스럽다면, 위와같은 작업을 할 수 없을 것이다. git은 거의 모든 작업을 local repository에서 수행하기 때문에 속도가 빠르다. 심지어 branch를 만들때도 remote server가 없어도 된다. 나중에 설명 하겠지만 git의 branch는 commit을 가르키는 포인터일 뿐이고, 그렇기 때문에 branch를 만드는 것 또한 빠르고 작은 용량으로 가능하다.

### Distributed
분산 저장소는, 모든 사용자들이 온전한 git repository를 가진다는 의미이다. 내가 진행중인 프로젝트를 git을 이용하여 내려받는다면, 나는 remote repository와 동일한 repository를 내 local에 가져오게 된다. 이말을 바꿔말하면 단지 repository를 가져오는 것만으로 source, commit hitsotry등 모든 요소를 가져오게 된다는 말인다. 최악의 경우 remote server의 장애로 모든 데이터를 날렸다고 하더라도, 다른 사용자의 repository를 이용해 복원 할 수 있다는 말인다.

그리고, 개발자들은 각자의 repository에서 작업하고(네트워크 없이) 작업이 끝난경우 remote repository로 push하여 개발 할 수 있고, git을 활용하여 업무의 workflow를 유연하게 적용 할 수 있다.
![Integration Manager Workflow](https://git-scm.com/images/about/workflow-b@2x.png)
출처 : https://git-scm.com/images/about/workflow-b@2x.png

### Data Assurance
git은 관리하는 모든 파일을 암호화하여 보관하고, 체크썸을 이용하여 파일의 유효성을 보장한다. 그리고 각 commit에는 commit 아이디가 부여되여 관리 되기 때문에 hitory 관리에도 유용하게 적용 할 수 있다.
### Staging Area
처음 git을 접하면 어렵게 느껴지는 부분인데 git에는 다른 VCS에는 없는 `staging area`가 있다.(index라고도 부름) 
![Git staging area](https://git-scm.com/images/about/index1@2x.png)
출처 : https://git-scm.com/images/about/index1@2x.png

파일들은 working directory에 있다. 이 상태에서 a.txt파일을 수정하면 파일의 상태가 modified로 바뀌고, 예상하는 바와같이 commit을 하면 a.txt가 commit 될 것처럼 보인다. 하지만 git에서는 이렇게 동작하지 않는다. modiried 상태인 a.txt파일을 staging area로 등록해야만 commit에 포함된다. 즉 git은 stating area의 스냅샷을 commit으로 기록하는 것이다.

### Free and Open Source
git은 [GNU General Public License version 2.0](http://opensource.org/licenses/GPL-2.0) 라이선스 정책을 따르고 있으며, 모든 사용자에게 무료로 제공된다. 주의할 점은 git의 [로고](http://git-scm.com/downloads/logos)는 다른 [라이선스 정책](http://git-scm.com/trademark)을 따른다는 것이다.

## git 설치
git을 설치하는데는 큰 어려움이 없다. Linux와 Mac OS X, Windows의 버전이 조금씩 다른데, [Downloads](http://git-scm.com/downloads) 페이지에서 각 OS별 download를 제공한다.

### Windows에 설치
Windows의 경우 download 페이지에 가면 설치파일이 제공된다. 다운로드받아 설치하는것으로 쉽게 가능하다. 설치 후 git bash를 이용하여 사용하면 된다.
### Linux에 설치
Ubuntu의 경우 apt-get(pakage manager)를 이용하여 쉽게 설치할 수 있다. 
```bash
$ apt-get install git
```
### Mac에 설치
Mac의 경우에도 download 페이지에서 installer를 제공하기 때문에 손쉽게 설치 가능하다.

어떤 OS든 git 설치가 되었는지 확인하려면 git의 버전을 보는 명령어를 실행하여 현재 git의 버전을 확인할 수 있다.
```bash
$ git --version
```
![git version](https://crynut84.github.io/images/posts/install-git/git-version.png)
