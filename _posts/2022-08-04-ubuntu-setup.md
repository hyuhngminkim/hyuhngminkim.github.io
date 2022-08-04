---
title: 'Ubuntu Setup'
date: 2022-08-04
permalink: /posts/2022/08/ubuntu-setup/
tags:
  - linux
  - ubuntu
---

많은 시행착오를 거쳐서 데스크탑에 우분투를 설치했습니다. 
참고로 우분투 버전은 20.04, 리눅스 커널 버전은 5.4.0-21-generic입니다. 
이번 포스팅에서는 우분투를 처음 설치했을 때 환경설정 과정을 기록하고자 합니다. 

# apt

가장 먼저 apt를 업데이트 해 줍니다.
이 과정은 수시로 해 주는 것이 좋습니다. 

```console
$ sudo apt-get update
$ sudo apt-get upgrade
```

저는 우분투를 하드디스크에 설치해서 그런지 시간이 정말 많이 걸렸습니다.
아무래도 처음 운영체제를 설치하고 하는 업데이트라 그런 것 같습니다. 

# Google Chrome

가장 먼저 웹 브라우저를 구글 크롬으로 바꿨습니다. 
[다운로드 링크](https://www.google.com/intl/ko_kr/chrome/)에 방문해서 64비트 ``.deb``버전을 받으면 됩니다. 
받은 다음 터미널에서 파일이 다운로드된 위치로 이동한 다음 다음 명령어를 실행합니다. 

```console
$ sudo apt install ./google-chrome-stable_current_amd64.deb
```

# 한글 키 설정

저는 기본 언어 설정을 영어로 설치했기 때문에 한글 언어 패키지를 따로 설치할 필요가 있었습니다.
이 블로그가 큰 도움이 되었습니다. 

출처: *[https://shanepark.tistory.com/231](https://shanepark.tistory.com/231)*

# vim

저는 코딩을 편하게 배웠기 때문에 vi보다는 vim이, vim보다는 vscode가 더 편합니다. 
먼저 vim부터 설치해 줍니다. 

```console
$ sudo apt-get install vim
```

설치가 되면 vim의 다양한 기능들을 설정할 수 있습니다. 
다음 블로그 글의 방법을 따랐습니다. 

출처: *[https://gabii.tistory.com/entry/Ubuntu-vim-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%84%A4%EC%A0%95](https://gabii.tistory.com/entry/Ubuntu-vim-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%84%A4%EC%A0%95)*

```console
$ vi ~/.vimrc
```

설정 파일을 연 후 ``i``를 쳐서 편집 모드로 들어가서 다음 내용을 적어 줍니다. 

```console
set number    " line 표시
set ai    " auto indent
set si " smart indent
set cindent    " c style indent
set shiftwidth=4    " 자동 공백 채움 시 4칸
set tabstop=4    " tab을 4칸 공백으로
set ignorecase    " 검색 시 대소문자 무시
set hlsearch    " 검색 시 하이라이트
set nocompatible    " 방향키로 이동 가능
set fileencodings=utf-8,euc-kr    " 파일 저장 인코딩 : utf-8, euc-kr
set fencs=ucs-bom,utf-8,euc-kr    " 한글 파일은 euc-kr, 유니코드는 유니코드
set bs=indent,eol,start    " backspace 사용가능
set ruler    " 상태 표시줄에 커서 위치 표시
set title    " 제목 표시
set showmatch    " 다른 코딩 프로그램처럼 매칭되는 괄호 보여줌
set wmnu    " tab 을 눌렀을 때 자동완성 가능한 목록
syntax on    " 문법 하이라이트 on
filetype indent on    " 파일 종류에 따른 구문 강조
set mouse=a    " 커서 이동을 마우스로 가능하도록
```

세부적인 설정은 개인의 취향에 따라 바꿔줍시다. 

# VSCode

다음으로 vscode 설치를 해 줍니다. 
이 블로그에 설명이 깔끔하게 되어 있습니다. 
그대로 따라해 줍니다. 

출처: *[https://chunggaeguri.tistory.com/entry/Ubuntu-2004-Visual-Studio-Code-%EC%84%A4%EC%B9%98%EB%B0%A9%EB%B2%95](https://chunggaeguri.tistory.com/entry/Ubuntu-2004-Visual-Studio-Code-%EC%84%A4%EC%B9%98%EB%B0%A9%EB%B2%95)*

## VSCode 설정

vsc를 처음 설치하고 이것저것 익스텐션을 받습니다. 
저는 기본적으로 Python, C/C++, Docker정도는 기본으로 받아 두고 색 설정을 하는데요.
애용하는 두 가지가 확장명에 따라 파일 아이콘을 바꿔주는 Material Icon Theme과 vsc 화면 색 설정을 도와주는 Community Material Theme입니다. 
그런데 Material Theme으로 화면 색을 바꾸니까 화면 상단의 메뉴바가 색이 바뀌지 않아 어색했습니다. 
화면을 검은색으로 하든 청록색으로 하든 메뉴바는 항상 흰색이어서 익숙해지지가 않았습니다.
이거에 익숙해지기를 기대하느니 바꿔버리는게 낫다고 생각해서 테마에 맞게 색이 바뀌도록 설정했습니다. 

출처: *[https://github.com/microsoft/vscode/issues/105338](https://github.com/microsoft/vscode/issues/105338)*

> 화면 좌하단 Manage > Settings > ``window.titleBarStyle`` 검색 > ``native``에서 ``custom``으로 변경

한번 vsc를 재시작하면 설정이 적용되어 사용자가 설정한 테마에 따라 메뉴바 색깔이 바뀌게 됩니다. 

# Git & Github Authentication

일단 git을 설치하는 것은 굉장히 간단합니다. 
일반적인 패키지를 설치하는 것과 동일하기 때문입니다. 

```console
$ sudo apt install git
```

이렇게 git 설치를 하고 나면 기본적인것은 마무리가 된 건데, 저는 조금 더 설정을 하고 싶었습니다. 
왜냐하면 이렇게 git 설치만 하면 매번 push를 하거나 pull을 할 때 인증을 해야 되기 때문입니다. 

## Solution 1

일차적인 해답은 다음 링크를 참고했습니다. 

출처: *[https://stackoverflow.com/questions/35942754/how-can-i-save-username-and-password-in-git](https://stackoverflow.com/questions/35942754/how-can-i-save-username-and-password-in-git)*

```bash
$ git config --global credential.helper store
```

위 명령어를 친 다음에 계정 인증이 필요한 작업을 하면 해당 인증을 기기에 저장합니다. 
그리고 다음에 사용자 인증이 필요한 경우 저장된 인증을 가져와서 사용하기 때문에 토큰 입력 등의 작업을 할 필요가 없어집니다. 

이 방법의 문제는 사용자 인증 정보를 평문으로 저장한다는 것입니다. 
따라서 해당 기기를 사용하는 다른 유저, 혹은 해당 기기에 접근할 수 있는 다른 프로그램 등이 사용자 인증 정보를 쉽게 취득해 사용할 수 있다는 위험이 있습니다. 

### Revoking credential store

먼저 해당 설정이 적용되어 있는지를 확인합니다. 
``.git``이 위치한 디렉토리에서 다음 명령어를 실행합니다. 

```bash
$ git config --list
```

제 경우에는 가장 윗줄에 다음과 같은 내용이 있었습니다. 

```
credential.helper=store
...
```

이 설정을 해제하는 방법은 다음 명령어를 실행하는 것입니다. 

```bash
$ git config --global --unset credential.helper
```

이후 다시 ``config`` 파일을 보면 ``credential.helper=store`` 설정이 사라진 것을 확인할 수 있습니다. 

## Solution 2 (GCM)

출처: *[https://stackoverflow.com/questions/36585496/error-when-using-git-credential-helper-with-gnome-keyring-as-sudo/40312117#40312117](https://stackoverflow.com/questions/36585496/error-when-using-git-credential-helper-with-gnome-keyring-as-sudo/40312117#40312117)*

두 번째 해결책은 우선 git 버전이 2.27 이상이어야 사용할 수 있습니다. 
우선 git을 업그레이드 해줍시다. 

### Upgrading git

출처: *[https://somjang.tistory.com/entry/Git-Ubuntu%EC%97%90%EC%84%9C-git%EC%9D%84-%EC%B5%9C%EC%8B%A0%EB%B2%84%EC%A0%84%EC%9C%BC%EB%A1%9C-%EC%97%85%EA%B7%B8%EB%A0%88%EC%9D%B4%EB%93%9C-%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95](https://somjang.tistory.com/entry/Git-Ubuntu%EC%97%90%EC%84%9C-git%EC%9D%84-%EC%B5%9C%EC%8B%A0%EB%B2%84%EC%A0%84%EC%9C%BC%EB%A1%9C-%EC%97%85%EA%B7%B8%EB%A0%88%EC%9D%B4%EB%93%9C-%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)*

지금 제 git 버전은 다음으로 확인할 수 있습니다. 

```bash
$ git --version
```

저는 2.25.1 버전으로 나옵니다. 
다음 명령어를 통해서 업그레이드 해 줍니다. 

```bash
$ git add-apt-repository ppa:git-core/ppa -y
$ sudo apt-get update
$ sudo apt-get install git -y
```

다른 포스팅에서 언급한 적이 있는데, ``-y`` 플래그는 설치하는 과정에서 나오는 모든 '예/아니오' 질문에 대해 '예'로 답하겠다는 의미입니다. 
대표적으로 '이 프로그램을 설치하면 xxMB의 디스크 용량을 사용하게 됩니다. 계속하시겠습니까?' 등이 있습니다. 
어차피 설치는 할 것이기 때문에 질문에 대답하는 것이 무의미할 때 사용하면 좋은 플래그입니다. 

이제 git 버전을 확인하면 가장 최신 버전이 설치되어 있을 것입니다. 
저는 2.37.1 버전으로 출력됩니다. 

### Git Credential Manager (GCM)

[Link to GCM](https://github.com/GitCredentialManager/git-credential-manager)

> Git Credential Manager (GCM) is a secure Git credential helper built on .NET that runs on Windows, macOS, and Linux. 

Git 인증 매니저는 보안이 뛰어난 git 인증 도우미라는 다소 재귀적인 설명이지만 이름부터 직관적이긴 합니다. 
말 그대로 git의 인증을 돕는 역할을 하는데, 다른 인증 도구들을 사용해보지 않아서 어떤 차이가 있는지는 정확히 모르겠습니다. 
우선 설치 방법을 살펴보겠습니다. 

먼저 [다운로드 링크](https://github.com/GitCredentialManager/git-credential-manager/releases)에서 운영체제와 아키텍쳐에 맞는 설치 파일을 받습니다. 
저는 ``gcm-linux_amd64.2.0.785.deb`` 파일을 받았습니다. 

다음으로는 설명서를 그대로 따라하면 됩니다. 
저는 ``.deb`` 파일이 Downloads 폴더 안에 있었기 때문에 다음과 같이 명령어를 실행했습니다. 

```bash
$ # sudo dpkg -i <path-to-package>
$ sudo dpkg -i ./Downloads/gcm-linux_amd64.2.0.785.deb
$ git-credential-manager-core configure
```

이렇게 하고 나면 설정은 끝이 납니다. 