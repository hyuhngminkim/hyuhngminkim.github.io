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
sudo apt-get update
sudo apt-get upgrade
```

저는 우분투를 하드디스크에 설치해서 그런지 시간이 정말 많이 걸렸습니다.
아무래도 처음 운영체제를 설치하고 하는 업데이트라 그런 것 같습니다. 

# Google Chrome

가장 먼저 웹 브라우저를 구글 크롬으로 바꿨습니다. 
[다운로드 링크](https://www.google.com/intl/ko_kr/chrome/)에 방문해서 64비트 ``.deb``버전을 받으면 됩니다. 
받은 다음 터미널에서 파일이 다운로드된 위치로 이동한 다음 다음 명령어를 실행합니다. 

```console
sudo apt install ./google-chrome-stable_current_amd64.deb
```

# 한글 키 설정

저는 기본 언어 설정을 영어로 설치했기 때문에 한글 언어 패키지를 따로 설치할 필요가 있었습니다.
이 블로그가 큰 도움이 되었습니다. 

출처: *[https://shanepark.tistory.com/231](https://shanepark.tistory.com/231)*

# vim

저는 코딩을 편하게 배웠기 때문에 vi보다는 vim이, vim보다는 vscode가 더 편합니다. 
먼저 vim부터 설치해 줍니다. 

```console
sudo apt-get install vim
```

설치가 되면 vim의 다양한 기능들을 설정할 수 있습니다. 
다음 블로그 글의 방법을 따랐습니다. 

출처: *[https://gabii.tistory.com/entry/Ubuntu-vim-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%84%A4%EC%A0%95](https://gabii.tistory.com/entry/Ubuntu-vim-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%84%A4%EC%A0%95)*

```console
vi ~/.vimrc
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



