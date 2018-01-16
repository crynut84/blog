---
title: git 기초 명령어
categories:
  - git
tags:
  - git
date: 2015-06-19 08:52:48
thumbnail:
---

[git을 설치](http://crynut84.github.io/2015-06-16/install-git.html)했다면 내 로컬에 완전한 git repository를 가질 수 있다는 것을 의미한다. 그럼 git을 이용해 어떻게 repository를 만들고 관리 할 수 있는지 기초 명령어를 알아보자.

아래와 같은 명령어를 다룰 것이다.

 - [git config](#git-config) 
 - [git init, git-clone](#git-init,-git-clone) 
 - [git status](#git-status) 
 - [git add](#git-add) 
 - [git diff](#git-diff) 
 - [git commit](#git-commit) 
 - [git reset](#git-reset) 
 - [git rm](#git-rm) 
 - [git mv](#git-mv) 
 - [git stash](#git-stash) 

이 11가지 명령어를 배우면 우리는 git을 사용해 프로젝트 파일의 생성, 수정, 삭제, 히스토리 관리를 할 수 있게 된다.

### git config
git을 설치하고 나서 제일 처음 할 일은 환경 설정을 하는 것이다. `config`라는 명령어로 환경 설정을 할 수 있는데 git의 환경 설정은 다음의 3곳에 저장된다.

 - /etc/gitconfig
  - `git config --system` 옵션을 주었을 때 사용되는 파일
  - 시스템의 모든 사용자, 모든 repository에 적용
 - ~/.gitconfig
  -`git config --global` 옵션을 주었을 때 사용되는 파일
  - 홈디렉터리에 위치한 것으로 볼 수 있듯이 해당 시스템 사용자에게 적용
 - ./.git/config
  - 각 git repository에 위치한 설정 파일
  - repository 별 설정을 저장한다.

git의 설정은 IIS의 web.config 처럼 system -> global -> repository 순으로 상속된다.

##### 사용자 설정
처음 설정이니 사용자 정보를 설정하여 보자. Linux나OS X는 terminal을 실행하고, Windows라면 git bash를 실행한다.
>$ git config --global user.name "Hojin, Jun"
>$ git config --global user.email "crynut84@gmail.com"

사용자명과 이메일을 설정하였다. git은 commit할 때 이 정보를 사용한다. `--global` 옵션에서 알 수 있듯이 시스템의 사용자에게 적용되는 옵션이다. `--global` 옵션을 빼고 각 repository 별로 설정 할 수 도 있다.

`git config --list`로 모든 설정을 확일 할 수있고. `git config user.name`처럼 특정 설정을 확인 할 수도 있다.

### git init, git clone
git repository를 만드는데는 2가지 방법이있다. 하나는 내 로컬의 디렉터리를 git repository로 만드는 것이고 원하는 디렉터리에서 아래 명령을 실행하기만 하면 된다.

```bash
$ git init
```

이 명령을 실행하면 **.git** 이라는 디렉터리가 만들어지는데 이안에 있는 내용이 git이 repository를 관리하기 위한 파일이고 이후의 저장소 내부의 변화를 감지하기 시작한다. 그리고 `git init` 명령을 실행한 디렉터리를 **working directory**라고 부른다.

다른 하나는 이미 만들어져 있는 git repository를 `clone`하는 것이다.

```bash
$ git clone [url]
```

clone 명령어를 이용해 가져 올 수 있는데 url은 repository의 주소이다. [github](https://github.com)를 기준으로 다음과 같이 clone 할 수 있는 url을 제공해 준다.
![github clone url](http://crynut84.github.io/images/posts/basic-command-git/github-clone-url.jpg)

### git status
`git status` 명령으로 현재 repository의 현재 상태를 확인 할 수 있다. `git init` 명령어로 생성한 repository의 상태를 확인해보자.
!(git init)[https://crynut84.github.io/images/posts/basic-command-git/git-init.jpg]
branch가 master(git의 기본 branch명)인 것과 commit이 없으니 파일을 생성/복사한 후 `git add` 명령으로 트래킹하라는 메세지를 볼 수 있다.

a.txt라는 빈 텍스트 파일을 생성한 후 `git status` 명령어로 확인 해보면 아래와 같이 **Untracked files** 목록에 a.txt 파일을 볼 수 있다.
![git status after add file](https://crynut84.github.io/images/posts/basic-command-git/git-status-after-add-file.jpg)
이 상태를 이해하기 위해서는 먼저 git의 working directory에 있는 파일의 라이프사이클에 대해 알 필요가 있다.
![git file life cycle](https://crynut84.github.io/images/posts/basic-command-git/lifecycle.jpg)

위의 그림에서 알 수 있듯이 파일은 관리되는 파일(Tracked)과 관리되지 않는 파일(Untracked)로 나뉜다.

 - Tracked
 파일의 상태를 감시한다. 파일의 상태를 아래 3가지로 분류하여 관리한다.
 - Unmodified
 파일의 변경이 없는 상태
 - Modified
 파일이 수정된 상태
 - Staged
 Modified 상태의 파일을 commit 하려면 Stated 상태로 만들어야한다.
 - Untracked
 파일의 상태를 감시하지 않는다.

이제 앞서 추가한 a.txt 파일이 **Untracked** 상태인것이 이해 될 것이다. 우리가 처음으로 파일을 추가하면 Untracked 상태로 추가된다.

### git add
그럼 a.txt파일을 `git add` 명령어로 추가해보자. git add [파일명]으로 추가 할 수 있다.
>$ git add a.txt

![git stated file status](https://crynut84.github.io/images/posts/basic-command-git/git-stated-file.jpg)
이렇게 Untracked 상태인 파일을 add하면 Stated 상태가 된다. 앞서 설명했듯이 Staged 상태의 파일은 commit에 포함되는 대상이 된다.

아직 배우지 않았지만 `git commit` 명령어를 이용하여 commit을 해보자.

```bash
$ git commit -m 'first commit'
[master (root-commit) 5488e51] first commit
1 file changed, 0 insertions(+), 0 deletions(-)
create mode 100644 a.txt
```

1개의 파일(a.txt)가 commit 된것을 확인 할 수 있다. 다시 `git status`로 상태를 확인해보면 commit 할 것이 없으며 working directory도 클린하다고 알려준다.

```bash
$ git status
On branch master
nothing to commit, working directory clean
```

이 상태에서 a.txt 파일을 수정해 보자. 수정하면 자연스럽게 modified 상태로 바뀔 것 임을 예상 할 수 있다.

```bash
$ echo '파일 수정' > a.txt
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	**modified:   a.txt**

no changes added to commit (use "git add" and/or "git commit -a")
```

이제 `git add` 명령을 실행하면 a.txt는 다시 Staged 상태가 되고, commit의 대상이 된다. 즉 `git add`명령은 *Untracked 상태이거나 Modified 상태인 파일을 Staged 상태*로 바꾸는 명령어이다.

Staged 상태에 대해 더 자세히 알아보기 위해 `git add'하고 파일을 한번 더 수정해 보자.

```bash
$ echo '파일을 한번 더 수정' > a.txt
$ git status
On branch master
**Changes to be committed:**
  (use "git reset HEAD <file>..." to unstage)

	modified:   a.txt

**Changes not staged for commit:**
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   a.txt
```

a.txt의 상태가 두개가 되었다. 어떻게 된 것일까? 이 개념이 아주 중요한데, **Stated 상태라는 것은 `git add`할 당시의 파일을 스냅샷**이라는 것이다. 위에 있는 Stated 상태의 a.txt의 내용은 `git add`를 했을 당시 내용인 '파일 수정'이라는 문구가 있을 것이고 UnStated 상태의 a.txt 파일에 내용은 '파일을 한번 더 수정'이라는 문구가 들어 있을 것이다. 만약 이 상태로 `commit`을 수행하면 Staged 상태의 내용인 '파일 수정' 문구가 들어있는 a.txt의 스냅샷이 commit 된다.

마지막으로 수정한 정보까지 포함하여 commit 하고 싶다면 `git add` 명령으로 한번 더 a.txt를 추가 해 주면 된다.

추가로 일반적으로 프로젝트를 수행하면 여러 파일을 추가/수정 하는 일이 많을 텐데, 그런경우 `git add *.js` 나 `git add .` 같이 한번에 여러 파일을 add 하는 방법도 있다.

### git diff
위의 예에서 보았듯이 Staged 상태라는 것은 `git add` 할 당시의 파일의 스냅샷이다. 그런데 개발을 하다보면 `git add`한 파일을 무심코 다시 수정하고 그냥 commit하는 실수가 있을 것 같다.
`git diff`명령은 이럴 때 유용한 명령인데, Staging 영역(Stated된 스냅샷을 모아놓은 영역)과 Working Directory의 차이를 보여준다.
위의 예제에서 `git diff` 명령을 수행해 보자.

```bash
$ git diff
diff --git a/a.txt b/a.txt
index ecb3871..40efd75 100644
\--- a/a.txt
+++ b/a.txt
@@ -1 +1 @@
-파일 수정
+파일을 한번 더 수정
```
Staging 영역에는 '파일 수정'이라는 내용의 파일이 Working 영역에는 '파일을 한번 더 수정'이라는 내용의 파일이 들어있는 것을 볼 수 있다.
commit을 수행하기 전에는 항상 `git status`로 내가 포함 할 파일의 목록이 정확히 포함 되었는지 확인 하고 `git diff` 명령으로 staging 영역과 working 영역의 차이가 없는지 살피는 습관을 들인다면 commit을 실수하는 일을 줄 일 수 있을 것이다.

추가로 Staged 상태와 이전 commit의 상태를 비교해 보려면 `git diff --cached` 옵션을 붙여서 실행하면 된다.

```bash
$ git diff --cached
diff --git a/a.txt b/a.txt
index e69de29..ecb3871 100644
\--- a/a.txt
+++ b/a.txt
@@ -0,0 +1 @@
+파일 수정
```

이전 상태의 commit에서 a.txt는 빈파일이었으니 아무것도 나오지 않았고 현재 Stated 상태의 a.txt는 '파일 수정'이라는 문구가 추가로 들어갔다는 것을 볼 수 있다.

### git commit
드디어 git에서 가장 중요한 명령어 중 하나인 `git commit` 명령어를 소개 할 차례이다. commit은 하나의 작업단위(변경사항)를 local repository에 기록하는 것이다.

그럼 하나의 commit은 언제 만드는 것이 좋을까? 프로젝트를 수행함에 있어, 코드를 변경한다는 것은 어떠한 목적(요구사항)을 달성하기 위한 행위이다. 나는 이슈관리시스템(TFS)에서 하나의 Backlog item이나 Bug item을 할당 받으면 그것을 분석하여, 논리적으로 묶일 수 있는(혹은 다른 코드에 영향이 없을단위)로 쪼개서 하위 Task를 생성한다. 그리고 이 Task를 commit과 1:1로 맵핑 시킬 수 있도록 노력하는 편이다. 그렇기에 어떤 commit에는 100개의 파일이 추가/수정/삭제되어 있을 수도 있고, 어떤 commit에는 단하나의 파일만 수정되어 있을수도 있다. commit의 단위는 자유롭게 설정하여 사용 할 수 있기 때문에 어떻게 제한 할 지는 각 프로젝트의 상황이나 팀원간의 합의에 따라 적절하게 정하면 된다.
다만 commit을 작성하는데 참고가 될만한 글을 소개한다.
[커밋 메시지에 대해](http://haruair.com/blog/2683)라는 제목의 글인데 커밋메세지를 작성하는 방법에대해 토론한 내용을 정리한 글이다.
>변경 사항에 따라 로직을 이해할 수 있는 수준의 단일 커밋 단위로 쪼개야 한다.

커밋 메세지를 작성하는 방법에서 커밋의 단위를 어떻게 가져가야 할지 예측해 볼 수 있을 것이다.

위의 예제를 계속 이어가기 위해 a.txt를 stating 영역으로 add하고 git status로 변경 사항을 확인한다.

```bash
$ git add a.txt
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   a.txt
```

이 상태에서 `git commit' 명령을 수행하면 commit 메세지를 적을 수 있는 text-editor가 실행된다.
!(github clone url)[https://crynut84.github.io/images/posts/basic-command-git/git-commit-editor.png]
이 editor는 `git config --global core.editor` 명령으로 변경 가능하다. editor를 실행하면 여러줄의 commit 메세지를 작성 할 수 있고, 한줄의 짧은 메세지로 commit 할 경우에는 위의 예제에서 실행 했듯이 `-m' 옵션을 주어 짧은 commit을 실행 할 수도 있다.

```bash
$ git commit -m 'my commit message'
[master ac05efa] my commit message
 1 file changed, 1 insertion(+)
$ git status
On branch master
nothing to commit, working directory clean
```

모든 변경사항을 commit한 후 `git status`명령으로 상태를 확인하면 위와같이 working directory가 깨끗하다는 메세지를 확인 할 수 있다. 모든 변경사항이 commit 된 것이다. 한번 commit한 사항은 local repository에 저장되기에 언제든 조회,복구 할 수 있다. 특별한 일이 없는 한 commit된 것은 날아가지 않는다.(remote server에 push한 경우에는 더더욱) 하지만 commit 하지 않은 것은 언제든 잃어 버릴 수 있다.

또한 위의 링크에서도 알 수 있듯이 우리는 commit 메세지 작성을 잘 해야한다. commit 메세지가 잘 작성된 경우에는 commit 메세지를 확인 하는 것만으로도 프로젝트의 히스토리를 파악 할 수 있다.

추가로 `git add`하지 않은 Working영역의 Tracked file을 모두 커밋하고자 할때에는 `-a`옵션을 사용한다.

```bash
git commit -a -m 'commit all tracked files'
```

`-a` 옵션을 사용경우 git은 Working영역의 파일을 stating 영역에 넣고, commit하는 것과 동일한 결과를 준다.

### git reset
프로젝트 파일을 수정 할 때 어떠한 목적을 가지고(하나의 commit에 묶일 수 있도록) 파일을 수정하겠지만, 사람이 하는 일이 항상 마음 먹은대로되는 것은 아니다. a.txt파일을 수정하는 미션을 받고 수정하는 중에 b.txt파일을 추가 했다고 가정해 보자. 논리적으로 a.txt파일을 수정한것만 하나의 commit으로 묶어야한다면, a.txt 파일을 `git add`하여 Stating 영역에 넣고 commit 하면 된다. 그런데 실수로 b.txt파일도 Stating 영역에 들어갔다면 b.txt파일을 다시 working 영역으로 옮기는 방법이 필요하다.

이럴 때 'git reset'명령을 사용하면 된다. 실습을 위해 a.txt파일을 수정하고, b.txt파일을 새로 만들어 보자.

```bash
$ echo 'modify a.txt file' > a.txt
$ cat > b.txt
$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   a.txt
	new file:   b.txt
```

`git add *` 명령으로 모든 변경사항이 stating 영역에 포함되었다. 이 상태에서 commit하면 a.txt,b.txt두개의 파일이 commit 될 것이다. b.txt를 commit에서 제외하기 위해 아래와 같이 `git reset` 명령어를 수행해 보자.

```bash
$ git reset HEAD b.txt
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   a.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	b.txt
```

b.txt파일이 이전 상태로 돌아간 것을 확인 할 수 있다. 이처럼 `git reset`은  staging 영역에서 working 영역으로 되돌리는 역할을하고, 이를 이용해 commit이 포함될 파일을 조절 할 수 있다.

### git rm
git이 관리하는(Tracked) 파일을 삭제하려면 어떻게 해야할까? 윈도우의 탐색기나 OSX의 Finder에서 파일을 삭제하거나 Terminal에서 RM 명령을 통해 삭제하면 된다.

위의 예제에서 working directory를 clean한 상태로 만들고 이어서 실습해 보자.(clean하게 하려면 commit 하면 된다.)
a.txt파일만 있는 상황에서 terminal에서 rm 명령을 이용해 파일을 삭제한다.

```bash
$ rm a.txt
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    a.txt
```

git은 파일의 삭제도 훌륭하게 추적하여 파일이 삭제되었음을 알려준다. 이 상태에서 `git add`명령어로 staging 영역에 추가하고 `git commit`하면 파일이 삭제된 스냅샷이 git repository에 저장 될 것이다.(앞서 배웠듯이 `git -a commit`명령으로 한번에 add+commit 할 수도 있다.)

git에서도 `git rm`이라는 명령어를 제공한다. 그냥 삭제해도 git이 알아서 인식하는데 왜 별도의 명령을 제공하는 것일까? 그냥 파일을 삭제해도 되지만 명령어는 몇가지 기능이 더 있다.
기본적으로 `git rm` 명령을 통해 파일을 삭제하면 파일을 삭제하고 자동으로 staging 영역으로 넣어준다.

```bash
$ git rm a.txt
rm 'a.txt'
$ ls -l
total 0
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	deleted:    a.txt
```

그리고 이미 staging 영역으로 들어간 파일을 물리적으로 삭제하면 다음과 같이 2개의 상태를 가지게 된다.

```bash
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	deleted:    a.txt
   new file:   b.txt

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    b.txt
```

b.txt가 새로 생성되었고, 삭제된 상태이다. git은 실수로 삭제하는 것을 막기위해서 이런 방법을 사용한다. `git rm -f` 옵션을 사용하면 바로 삭제처리도 가능하다.

### git mv
`git mv`명령은 파일을 이동하는 명령이다. 이동이라는 것은 경로를 바꾼다는 뜻이며, 파일의 이름을 변경하는 행위와도 같다. 사용법도 아주 쉽다. 아래를 참조하자.

```bash
$ git mv a.txt c.txt
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	renamed:    a.txt -> c.txt
```

### git stash
TFS를 사용할 때 Shelve(보류)라는 기능을 유용하게 사용했었다. 이런 시나리오는 생각해보자. 어떤 Task를 할당받아 열심히 개발하는 중에 치명적인 버그가 발견되었다. 급하게 Hot Patch를 내놓아야 하는 상황에서 선택 할 수 있는 방법은 두가지이다.

하나는 현재 개발 중인 Task를 최대한 빨리 마무리하고, Hot Patch를 개발하는 방법이고, 또하나는 현재 진행하던 Task를 버리고(되돌리고) 급한 패치부터 개발하는 방법이다. Shelve는 이럴때 유용하게 사용 할 수 있는데 진행하던 Task를 보류시켜 놓고(어딘가에 저장) 이전 상태로 돌아가 Hot Patch를 만들어 checkin한 후 다시 보류된 작업을 꺼내와서 Task를 이어서 진행하는 방법이다.

git에도 `git stash` 명령어를 통해서 위와같은 시나리오에 대응 할 수 있다. 파일을 변경한 상태에서 (즉 워킹 디렉토리가 clean하지 않은 상태) `git stash` 명령을 사용하고 `git staus`명령으로 상태를 확인하면 아래와 같이 clean 한 상태가 된다. 이때 위의 시나리오처럼 다른 작업을 할 수 있는 것이다.

```bash
$ echo 'change c.txt' > c.txt
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   c.txt

no changes added to commit (use "git add" and/or "git commit -a")
rynut84@ubuntu:~/work/git-test git stash
Saved working directory and index state WIP on master: da3fe70 rename a.txt -> c.txt
HEAD is now at da3fe70 rename a.txt -> c.txt
crynut84@ubuntu:~/work/git-test$ git status
On branch master
nothing to commit, working directory clean
```

또한 stash 된 목록을 `git stash list` 명령으로 확인 할 수 있고, stashing 한 목록을 복원하려면, `git stash pop' 명령을 이용하면 된다.

총 11가지 명령으로 git의 local repository를 다루는 방법을 배웠다. git이 가진 명령어는 지금 소개한것보다 훨씬 많지만(옵션까지 하면 더 많다) 이정도만 알아도 크게 불편한 점이 없었고, 더 자세한 기능은 그때그때 찾아보면서 쓰는 편이다.