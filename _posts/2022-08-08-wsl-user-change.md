---
title: 'WSL Username Configuration'
date: 2022-08-08
permalink: /posts/2022/08/wsl-username/
tags:
  - linux
  - ubuntu
---

잠깐동안 우분투를 네이티브로 설치했다가 다시 윈도우로 회귀했습니다. 
너무 윈도우가 느려지고 계속 오류가 나서 어차피 컴퓨터에 내용도 별로 없고 중요한 자료는 다 클라우드에 올라가있겠다, 그냥 포맷을 시켜버리기로 마음먹었습니다. 
포맷을 시키고 나니 이것저것 설정할 것이 조금 있더군요. 

가장 먼저 한 것은 git이나 vscode, Windows Terminal, WSL2 등을 설치한 것이었습니다. 
하지만 이 포스팅은 그런 큼직한 내용을 다루기 위한 것이 아닙니다. 
정말 사소한, WSL에서 구동하는 우분투 내의 기본 사용자 이름을 바꾸는 것에 관한 것입니다. 

우선 WSL을 설치하는 것은 정말 간단합니다. 
제가 예전에 할 때는 더 복잡했던 것 같은데, 이제는 PowerShell에 다음 명령어 한 줄만 쳐도 가능합니다. 

```bash
# PowerShell
$ wsl --install
```

그러면 설정부터 우분투 설치까지 알아서 다 해 줍니다. 
그리고 난 뒤, 컴퓨터를 재부팅을 하고 터미널을 키면 우분투 설정을 마무리하게 되죠. 
유저명과 비밀번호를 설정하는 것으로 말입니다. 

그런데 저는 여기서 정말 멍청한 실수를 했습니다. 
아무런 생각 없이 유저명을 ``user``로 해 버린 것입니다. 
그래서 너무 늦기 전에 유저명을 바꾸기로 했습니다. 

# Changing Username (Unnecessary)

출처: *[https://www.linuxuprising.com/2019/04/how-to-change-username-on-ubuntu-debian.html](https://www.linuxuprising.com/2019/04/how-to-change-username-on-ubuntu-debian.html)*


위 링크의 설명을 하나하나 따라가면 쉽습니다. 
먼저 임시 유저를 하나 만들고 sudo 권한을 부여합니다. 
저는 ``tempuser``라는 유저를 새로 만들었습니다. 

```bash
# Ubuntu
$ sudo adduser tempuser
```

그러면 여기서 이름이나 부서나 이메일같이 지금 저희에게는 쓸모없는 설정을 마구 물어봅니다. 
모두 설정할 필요 없이 엔터키를 눌러 스킵합니다. 
그리고 해당 유저에게 sudo 권한을 부여합니다. 

```bash
# Ubuntu
$ sudo usermod -aG sudo tempuser
```

이 다음에는 새로운 유저인 ``tempuser``로 로그인을 해야 합니다. 
저는 WSL에서 우분투를 쓰면서 이 부분이 조금 곤란했습니다. 
어떻게 유저를 변경해야 하는지 몰랐기 때문입니다. 

# Changing Current User

WSL 우분투에서 사용자를 바꾸기 위해서는 다시 윈도우 명령 프롬프트로 들어가야 합니다. 
명령 프롬프트에서 다음 명령어를 쳐 봅시다. 

```bash
# CMD Prompt
$ ubuntu config --default-user root
```

그러면 WSL에서 우분투 터미널을 킬 때 로그인되는 사용자가 ``root``일 것입니다. 
여기서 앞서서 ``tempuser`` 사용자를 만드는 것이 왜 불필요한 것인지 알 수 있습니다. 
어차피 우리가 사용하는 WSL에 대한 루트 권한이 있기 때문에 굳이 별도의 유저를 만들 필요가 없는 것입니다. 

# Changing Username

그러면 이제 ``root`` 사용자로 우분투를 켠 뒤 어떻게 설정하면 되는지 알아보도록 하겠습니다. 

가장 먼저 기존 사용자 이름으로 실행되고 있던 프로세스를 죽여야 합니다. 
다음 명령어를 통해 현재 실행되고 있는 프로세스를 찾습니다. 

```bash
# Ubuntu
$ ps -ef
```

그러면 현재 실행되고 있는 프로세스를 실행시킨 사용자 이름(UID)와 프로세스 번호(PID)를 비롯한 정보를 확인할 수 있습니다. 
여기서 우리가 바꾸고자 하는 사용자가 실행중인 프로세스 번호를 죽입니다. 
다음 명령어를 실행하면 됩니다. 

```bash
# Ubuntu
$ kill <pid>
```

애초에 우리는 지금 루트 권한으로 로그인한 것이기 때문에 권한을 획득할 필요가 없습니다. 
바꾸고자 하는 사용자가 실행중인 프로세스를 위 명령어를 통해 모두 죽여서 해당 사용자가 실행중인 프로세스가 없을 때 비로소 사용자 이름을 바꿀 수 있습니다. 

```bash
# Ubuntu
$ usermod -l <newusername> -d /home/<newusername> -m <oldusername>
```

그러면 비로소 사용자 이름을 바꿀 수 있는 것입니다. 
잊지 말고 명령 프롬프트에서 기본 접속 아이디를 바꿔놓도록 합시다. 

```bash
# CMD Prompt
$ ubuntu config --default-user <newusername>
```

이렇게 설정하면 우리가 바꾼 사용자 이름으로 로그인할 수 있게 됩니다. 