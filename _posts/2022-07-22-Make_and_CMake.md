---
title: 'CMake Basics'
date: 2022-07-22
permalink: /posts/2022/07/cmake-basics/
tags:
  - cmake
---

# Introduction

원래는 [LevelDB](https://github.com/google/leveldb)의 코드를 하나하나 뜯어보면서 어떻게 시스템이 구성되어 있는지 확인하는 포스팅을 작성할 계획이었습니다. 
당연히 하나로는 택도 없고 몇 개에 나누어서 할 생각이었습니다. 
그런데 다짜고짜 코드를 뜯어서 보기 시작하는 것 보다 ``CMakeLists.txt`` 파일을 보면서 이 프로젝트가 어떻게 빌드되는지 확인하는 것이 도움이 될 것 같아서 CMake부터 공부를 하기로 마음먹었습니다. 

이 포스팅은 유튜브의 [Code, Tech, and Tutorials](https://www.youtube.com/c/CodeTechandTutorials) 채널의 영상을 보고 정리한 것입니다. 

# Understanding the Basics

출처: *https://www.youtube.com/watch?v=nlKcXPUJGwA&list=PLalVdRk2RC6o5GHu618ARWh0VO0bFlif4*

## Make?

CMake를 알아보기 전에 Make를 알 필요가 있습니다. 
예전부터 과제나 프로젝트를 하면서 Makefile 같은 파일들이 있다는 것은 알고 있었지만 문서에서는 그냥 빌드만 하는 용도로 쓰고 크게 중요하게 보지 않았기 때문에 정확히 어떤 것을 하는 것인지 잘 몰랐습니다. 
지금부터 간단하게 Make가 어떤 역할을 하는지 알아보겠습니다. 

사실 Make의 개념은 단순합니다. 
우리가 코드를 작성했다고 할 때, 그 코드를 실행 가능한 실행 파일로 바꾸는 과정을 명시적으로 작성한 것입니다. 

어디선가 들어보셨을지도 모르지만, 작성한 코드를 실행 파일(executable)로 바꾸는 과정은 몇 가지의 단계를 거칩니다. 
코드를 어셈블리어(assembly language)로 번역하는 컴파일러(compiler), 어셈블리어를 기계어 형태의 오브젝트 코드(object code)로 번역하는 어셈블러(assembler), 오브젝트 코드와 사용된 함수를 제공하는 라이브러리를 연결하는 링커(linker) 등을 거쳐야 비로소 하나의 실행 파일이 만들어집니다. 
물론 이 포스팅에서는 설명을 위한 간단한 예시만을 사용할 것이지만, 프로젝트가 거대해지고 복잡해지면 작성된 코드와 헤더 파일들의 의존성을 해소하고 올바르게 빌드하는 것 만으로도 큰 힘이 듭니다. 
이 과정을 작성하고 유지, 보수가 가능하도록 만들어진 것이 Makefile입니다. 

예를 들어 보겠습니다. 
여기 반가운 C 프로그램이 하나 있습니다. 

```c
#include <stdio.h>

int main(void) {
    printf("Hello, world!\n");
    return 0;
}
```

이 프로그램을 빌드하려면 어떻게 했는지 기억하시나요?
다음과 같이 하면 됐습니다!

```bash
$ gcc -o main main.c
```

그러면 ``main``이라는 실행 파일이 하나 생기게 되죠. 
이 과정을 Make를 사용해서 하는 방법은 다음과 같습니다. 
우선 ``Makefile``이라는 제목의 파일을 하나 만들겠습니다. 
(이 이름은 대충 지은 것이 아니라 꼭 이 이름으로 만들어져야 합니다. ``Makefile`` 혹은 ``makefile``로 하셔야 합니다. )

```bash
$ touch Makefile
```

그리고 그 안에는 다음과 같이 적어줍시다. 

```makefile
default:
    gcc -o main main.c
```

``default``아래에 적히는 명령어는 Makefile이 처음 실행되었을 때 기본적으로 실행되는 명령어를 의미합니다. 
이렇게 간단하게 작성된 Makefile을 실행하는 명령어는 그 내용물만큼이나 간단합니다. 

```bash
$ make
```

그러면 우리가 작성한 ``gcc -o main main.c``가 그대로 실행되는 것이 보입니다. 
Makefile을 사용하는 이 방법 또한 ``main``이라는 실행 파일을 남기게 됩니다. 

이렇게 간단한 코드에는 Make를 사용하는 것이 쓸데없이 번잡한 일입니다. 
하지만 프로젝트가 점점 커지고 복잡해지면 Make를 사용해서 빌드 과정을 관리하는 것이 훨씬 직관적이고 효율적입니다. 

## CMake!

멀쩡한 Make를 두고 CMake를 왜 사용하느냐에 대한 해답은 제가 Make를 사용해본 적이 없어서 그 필요성이 와닿지가 않습니다. 
그래서 이리저리 검색을 한 결과 이렇게 요약을 할 수 있을 것 같습니다. 

> 프로젝트가 커질수록 Makefile의 의존성 관리를 하는 것이 쉽지 않아진다.

Make가 강력한 도구임에도 그것을 관리하기 쉽지 않아졌고, 그 관리를 쉽게 만들기 위해 개발된 것이 CMake라고 할 수 있겠습니다. 

### Installing CMake

먼저 CMake를 설치해 줍니다. 
CMake 다운로드 [링크](https://cmake.org/download/)로 이동해 최신 버전 압축 파일을 구해 봅시다. 
제 경우에는 다음과 같이 했습니다. 

```bash
$ wget https://github.com/Kitware/CMake/releases/download/v3.23.2/cmake-3.23.2.tar.gz
```

다음으로는 압축을 풀어 설치해 줍니다. 

```bash
$ tar -xvf cmake-3.23.2.tar.gz
$ cd cmake-3.23.2
$ ./bootstrap --prefix=/usr/local
```

저는 여기서 오류가 하나 발생했는데, ``OpenSSL``을 찾을 수 없다는 것이었습니다. 
다음과 같이 해결했습니다. 

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
$ apt-get install libssl-dev
```

이어서 설치를 진행해 줍니다. 

```bash
$ ./bootstrap --prefix=/usr/local
$ make

```

### Trying CMake

앞서 저희가 Make를 사용할 때는 ``Makefile``이라는 파일을 새로 만들었습니다. 
CMake도 마찬가지로 ``CMakeLists.txt``라는 파일을 만들어야 합니다. 
(이 이름 역시 바꾸지 않고 그대로 만들어야 합니다. )
그리고 다음과 같이 내용을 작성합니다. 

```cmakelist
# CMake에 필요한 최소 버전을 명시합니다. 
cmake_minimum_required(VERSION 3.13.4)

# 프로젝트 이름을 명시합니다. 
project(test)

# 실행 파일 이름을 명시합니다. 
add_executable(${PROJECT_NAME} main.c)
```