---
title: Windows10에서 Linux 개발환경 구축
categories:
  - null
tags:
  - null
date: 2018-01-10 07:58:25
thumbnail:
---

2년 정도 OS X를 개발환경으로 사용했더니, 얼마 전 이직한 회사의 Windows 10 환경이 낯설게만 느껴졌다. Shell이 제일 불편했고, 각종 오픈소스 문서에 나오는 설정을 따라 하는데 Windows의 경우 설명이 친절하지 않은 경우가 많았다. 

집에서는 Mac을 사용하다 보니 매번 다른 환경이 적응 안되기도 하고 남들 다 윈도우 PC 쓰는데 혼자만 Mac 사달라고 할 수도 없고 `git bash`로 근근이 버티고 있었는데, Windows 10에 `Windows Subsystem for Linux(WSL)`라는 반가운 기능이 생긴 걸 알게 되어 그 방법에 관해 설명한다. (아마도 윈도우는 공인인증서 쓸 때만 쓰다 보니 혼자만 몰랐던 듯..)

## 설치하기
#### 윈도우 빌드 정보 확인
`WSL` 기능을 사용하려면 Windows 10의 빌드 버전이 `14316` 이상이어야 한다. 버전 확인은 [설정] -> [정보] -> [OS 빌드]를 확인하자.
![image](https://user-images.githubusercontent.com/5028400/34939095-f3caa526-fa2d-11e7-8e1f-079f7fba37bd.png)

#### 개발자 모드 설정
[설정] -> [업데이트 및 보안] -> [개발자용] -> [개발자 모드] 선택
![image](https://user-images.githubusercontent.com/5028400/34939258-8d3473b8-fa2e-11e7-89c0-6af88e2f2eef.png)

#### 윈도우 기능 켜기
[제어판] -> 프로그램 및 기능 -> [Windows 기능 켜기/끄기] -> [Linux용 Windows 하위 시스템] 체크 후 재부팅
![image](https://user-images.githubusercontent.com/5028400/34939316-b99c0ea2-fa2e-11e7-8b39-f94a404b8ecc.png)

#### 리눅스 설치
`cmd`를 관리자권한으로 열고 프롬프트에 `bash`를 치면 아래와 같이 https://aka.me/webstore 로 가라는 메시지가 나온다.

```bash
C:\windows\system32>bash
Linux용 Windows 하위 시스템에 배포가 설치되어 있지 않습니다.
아래의 Windows 스토어에서 배포를 설치할 수 있습니다.
https://aka.ms/wslstore
계속하려면 아무 키나 누르세요...
```
브라우저에서 위의 주소로 접속하여 Store를 열고 마음에 드는 리눅스 배포판을 선택한다. Utuntu를 선택하여 다운로드해주었다.
![image](https://user-images.githubusercontent.com/5028400/34940910-c77e4516-fa34-11e7-9ba6-57a841f56271.png)

다운로드가 완료되면 `실행`을 눌러 설치한다.
![image](https://user-images.githubusercontent.com/5028400/34940992-28d8a2d4-fa35-11e7-8549-5d3eef8c6080.png)
설치가 진행되며 우분투에서 쓸 username과 password를 입력하면 곧바로 `bash` 셸을 만날 수 있다.

#### Hyper 설치

Windows 10의 기본 터미널 프로그램(`cmd`)은 칙칙한 게 별로다. OS X에서는 `iterm`을 사용했었는데 Windows 용으로는 나오지 않으니 [Hyper](https://hyper.is/)라는 Web 기반 기술로 만들어진 터미널을 설치하자.

Hyper는 오픈소스 터미널이고 다양한 OS를 지원한다. 그리고 무엇보다 예쁘다. 설치는 간단한데 [공식 사이트](https://hyper.is/)에 들어가서 다운로드하면 되고, 여러 가지 Theme과 plugin을 제공한다.

나는 아래와 같이 몇가지 설정(`ctrl + ,`)만 바꾸어 사용한다.

```json
    updateChannel: 'canary',
    shell: 'C:\\Windows\\System32\\cmd.exe',
    shellArgs: ['--login', '-i', '/c wsl'],
    plugins: [
            'hyper-material-theme',
    ],
```
![image](https://user-images.githubusercontent.com/5028400/34963214-b92a21f0-fa8a-11e7-9c13-19f6a3023998.png)

### ZSH와 Oh my zsh 설치
bash는 뭔가 밋밋하고, 자동완성도 별로라 linux 초보인 나를 더 힘들게 한다. zsh 셸로 갈아타 보자.

```bash
sudo apt-get install zsh
chsh -s /usr/bin/zsh # 기본 쉘 변경
```

zsh를 설치했으면 `oh my zsh`도 설치해서 더 강력한 셸로 만들자.
```bash
 wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh
```
#### 테마 설치
vi로 `~/.zshrc`를 열어 아래와 같이 테마를 변경한다. 다른 테마를 원하면 [여기](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)를 참고하자.
```bash
ZSH_THEME="agnoster"
```
`source ~/.zshrc` 명령으로 적용하면 아래와 같이 글자가 깨지는 것을 볼 수있다. agnoster 테마가 이모티콘을 사용하는데 폰트가 없어서 그런것이니 폰트도 설치하자.
![image](https://user-images.githubusercontent.com/5028400/34963948-6900b730-fa8e-11e7-96a8-f8fea4f86a67.png)

[DejaVu Sans Mono](https://github.com/powerline/fonts/tree/master/DejaVuSansMono)에서 폰트(.ttf)를 내려받고 더블클릭하여 윈도우에 설치하자.

그리고 hyper config(`ctrl + ,`)을 눌러 폰트를 변경하면 된다.
```bash
fontFamily: '"DejaVu Sans Mono for Powerline", ...'
```
![image](https://user-images.githubusercontent.com/5028400/34964380-a3f4848c-fa90-11e7-9768-bc051f403657.png)


### Python 개발환경 설치
WSL에서는 python 버전 관리를 위해 `[pyenv](https://github.com/pyenv/pyenv)`도 사용할 수 있다. 

#### pyenv
먼저 필요한 패키지를 설치한다. ([참고](https://github.com/pyenv/pyenv/wiki/Common-build-problems))
```bash
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev
```
다음 명령어로 pyenv를 설치한다.
```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```
그 후 `~/.zshrc` 파일을 열어 맨 아래 path를 적어준다.
```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

#### pyenv-virtualenv
아래 명령으로 설치한다.
```bash
git clone https://github.com/yyuu/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
```
그 후 `~/.zshrc` 파일을 열어 맨 아래 다음 구문을 적어준다.
```
eval "$(pyenv virtualenv-init -)"
```

간단한 사용 방법은 간단하고 편하다.
```bash
pyenv install 3.6.4 # python 3.6.4 버전 설치
pyenv virtualenv 3.6.4 myenv # 3.6.4 버전을 기반으로 myenv라는 가상환경 생성
pyenv shell myenv # myenv라는 가상환경 activate
pyenv versions # 설치된 python 버전 목록 출력
```

#### autoenv
autoenv까지 설치하면 유용한데, 특정 프로젝트 디렉터리에 진입하면 자동으로 스크립트를 실행시킬 수 있기 때문이다. 이 기능을 활용해 `pyenv shell xxxenv`와 같이 특정 python 환경을 자동으로 activate 할 수 있다.

까먹고 activate 안 한 경험 누구나 한 번쯤 있을 것이다.

다음의 명령으로 설치한다.
```bash
git clone git://github.com/kennethreitz/autoenv.git ~/.autoenv
```
그 후 `~/.zshrc` 파일을 열어 맨 아래 다음 구문을 적어준다.

```
source ~/.autoenv/activate.sh 
```

원하는 디렉터리에 `.env` 파일을 만들고 스크립트를 적어놓으면 그 디렉터리 접근 시 자동으로 스크립트가 실행된다.
`.env` 파일은 종종 다른 라이브러리의 환경설정 파일과 이름이 겹친다. 그래서 나는 `.autoenv`로 이름을 바꾸어 사용하는데 그러려면 `~/.zshrc` 파일에 추가한다.

```
export AUTOENV_ENV_FILENAME=".autoenv"
```

위에 추가한 `source ~/.autoenv/activate.sh` 보다 위에 추가해야 한다.

처음 디렉터리에 접근 시 1번은 확인하는 구문이 나온다. `y`를 누르면 지정한 스크립트가 실행되는 것을 볼 수 있다.
```
$ cd blog
autoenv:
autoenv: WARNING:
autoenv: This is the first time you are about to source /mnt/d/work/documents/blog/.autoenv:
autoenv:
autoenv:   --- (begin contents) ---------------------------------------
autoenv:     echo "hello"$
autoenv:
autoenv:   --- (end contents) -----------------------------------------
autoenv:
autoenv: Are you sure you want to allow this? (y/N) y
hello
```

### Node.js npm
역시 apt-get이 있으니 간편하다. 아래 스크립트의 `setup_x.x` 부분에 설치하고 싶은 버전을 적으면 된다.
```bash
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```
### docker 설치
WSL에는 아쉽게도 docker engine을 설치할 수 없는 것 같다.
윈도우에 docker engine을 설치하고 WSL에는 client만 설치하여 사용하는 방법을 선택하자.

```bash
curl -sSL https://get.docker.com/ | sudo sh
```
설치 후 `docker ps` 명령을 쳐보면 docker에 연결할 수 없다고 나온다.
```bash
$ docker ps
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```
윈도우 docker를 열고 Expose daemon on tcp://localhost:2375 without TLS 옵션을 선택한다.
![image](https://user-images.githubusercontent.com/5028400/34965836-84ec3ca6-fa9a-11e7-973c-7ce27389eb87.png)

그 후 `~/.zshrc`에 `DOCKER_HOST`를 다음과 같이 설정한다.
```
export DOCKER_HOST='tcp://0.0.0.0:2375'
```