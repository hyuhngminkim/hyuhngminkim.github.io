---
title: 'Installing PyFlink'
date: 2022-07-08
permalink: /posts/2022/07/installing_pyflink/
tags:
  - flink
---

[지난 포스팅](https://hyuhngminkim.github.io/posts/2022/07/trying_docker/)에서는 도커를 설정하는 과정을 다뤘습니다. 
처음으로 Dockerfile을 만들고, 베이스 이미지로 사용한 CentOS 위에 자바나 파이썬, 플링크같이 이것저것 올리는 과정을 기록했습니다. 
하지만 플링크를 어떻게 다루는지보다는 도커 설정과 실행시키는 것에 초점을 뒀기 때문에 그 이미지 그대로 도커 컨테이너를 실행시킨다면 제대로 작동하지 않습니다. 
~~이번 포스팅에는 플링크의 파이썬 API 문서를 살펴보며 어떻게 환경 설정을 하고 실행시키는 것인지 알아보도록 하겠습니다.~~
원래는 Flink에 대해 전체적으로 알아보려고 했는데, 설치하는 방법을 알아내는 데에만 너무 시간이 많이 소모되어 아예 이 포스팅을 설치하는 것으로 대체하기로 했습니다!

# References

[Apache Flink Python API Documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/docs/dev/python/overview/)

# Prerequisites

기본적으로 실행하기 위해 필요한 요구사항은 다음과 같습니다. 

- Java 11
- Python 3.6, 3.7, or 3.8

모두 지난 포스팅에서 만든 이미지에 포함된 요구사항이기 때문에 신경 쓸 필요가 없습니다!
사실 이 요구사항을 보고 만든 이미지이기 때문에 당연한 것이긴 합니다. 

# Installation

## Using [PyPI](https://pypi.org/project/apache-flink/)? (FAILED)

다음 코드를 통해 PyFlink를 설치합니다. 
마찬가지로, 이 역시 Dockerfile에 명시해서 우리가 만든 이미지에는 포함되어 있습니다. 

```bash
$ python -m pip install apache-flink
```

이렇게만 해서 됐으면 참 쉬웠을 텐데, 그렇게 쉽게 될 리가 없습니다. 
설치 뒤에 실행을 시키면 이런 오류가 계속 나타납니다. 

```
The flink-python jar is not found in the opt folder of the FLINK_HOME: /usr/local/lib64/python3.6/site-packages/pyflink
```

그래서 검색도 많이 해 보고 여러 방법을 찾아봤는데, PyFlink 압축 파일을 직접 받아서 설치하는 방법을 알게 되었습니다. 
도커를 통해 PyFlink를 실행하는 방법을 설명한 [공식 문서](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#using-flink-python-on-docker)에서 힌트를 얻었습니다. 
이 링크에 있는 코드의 마지막에 다음과 같은 줄이 있는데요. 

```docker
RUN pip3 install /apache-flink-libraries*.tar.gz && pip3 install /apache-flink*.tar.gz
```

여기서 제가 가졌던 의문은, 공식 문서에 설명된 Flink [다운로드 링크](https://flink.apache.org/downloads.html)를 따라 가서 바이너리 파일을 받으면 확장명이 ``.tgz``로 되어 있는 반면에 도커를 통한 PyFlink 실행 방법에는 확장명이 왜 ``.tar.gz``로 되어 있는가였습니다. 
또, 설명에는 분명 ``apache-flink-libraries``라는 파일이 있는데, 이런 파일을 저는 본 적이 없었습니다. 
그래서 앞선 다운로드 링크를 뒤져본 결과, Flink의 모든 배포가 있는 [링크](https://archive.apache.org/dist/flink/)를 찾을 수 있었고 그 안에 제가 찾던 파일들이 모두 있었습니다. 

## Using Docker? (FAILED)

이제 이 파일들과 그 링크를 통해 우리가 이전에 만든 Dockerfile을 수정할 수 있습니다. 
다음의 코드를 추가하면 됩니다. 

```dockerfile
RUN curl https://archive.apache.org/dist/flink/flink-1.13.5/python/apache-flink-1.13.5.tar.gz > apache-flink-1.13.5.tar.gz
RUN curl https://archive.apache.org/dist/flink/flink-1.13.5/python/apache-flink-libraries-1.13.5.tar.gz > apache-flink-libraries-1.13.5.tar.gz

RUN pip3 install /apache-flink-libraries*.tar.gz && pip3 install /apache-flink*.tar.gz
```

우리가 쓰고자 하는 Flink 1.13.5를 받아 각각의 압축 파일로 받은 뒤 설치한 압축 파일들을 설치하는 것입니다. 
이렇게 해서 되면 참 좋겠지만, 도커 이미지를 빌드하면 수백줄은 되는 에러 메시지가 나옵니다. 
수백 줄의 에러 메시지 중에서 핵심은 다음 한 줄입니다. 

```
unable to execute 'gcc': No such file or directory
```

에러 메시지의 전체 내용은 길지만 하고자 하는 말은 결국 gcc가 없다는 건데요, 그냥 yum을 통해 설치해주면 되는 문제입니다. 
다음 한 줄을 추가해주면 됩니다. 

```dockerfile
RUN yum install -y gcc
```

``-y`` 옵션은 까먹지 말고 꼭 넣어 줍시다. 
다시 이미지를 빌드하면 이전과 비교해도 뒤지지 않을 만큼의 에러 메시지가 출력됩니다. 
이번 에러 역시 내용은 길지만 핵심은 다음 한 줄입니다. 

```
fatal error: Python.h: No such file or directory
```

[이 링크](https://stackoverflow.com/questions/21530577/fatal-error-python-h-no-such-file-or-directory)에 우리가 찾는 답이 있습니다. 
Python dev의 헤더 파일과 라이브러리를 올바르게 설치하지 않았기 때문이라고 합니다. 
설치하면 해결될 문제라는 뜻이군요. 
바로 설치해 줍시다. 

```dockerfile
RUN yum install -y python3-devel
```

이렇게 하면 설치는 올바르게 되는데 역시 실행이 안됩니다. 
다른 방법을 찾아야 합니다. 

## Using Apache Arrow

### Dockerfile Configuration

[Apache Arrow](https://arrow.apache.org/)와 [Miniforge](https://github.com/conda-forge/miniforge)를 사용한 방법을 알아보겠습니다. 
연구실 선배가 인수인계로 남겨준 방법인데, 이 방식을 쓰지 않고 다른 방법으로 돌아가려고 하다가 사흘정도는 시간을 날린 것 같습니다. 
어떻게 하면 되는 건지 차근차근 기록을 남겨보려고 합니다. 
먼저 기본적으로 [지난 포스팅](https://hyuhngminkim.github.io/posts/2022/07/trying_docker/)과 같이 CentOS를 베이스로 하기 때문에 다음과 같이 베이스 이미지와 yum 설정을 설정합니다. 

```dockerfile
FROM centos:latest

RUN cd /etc/yum.repos.d/
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
RUN cd ~/..
RUN yum update -y
```

Arrow의 빌드 [문서](https://arrow.apache.org/docs/2.0/developers/cpp/building.html)를 보면 다음과 같은 요구사항이 있습니다. 

- A C++-enabled compiler. On Linux, gcc 4.8 and higher should be sufficient. 
- CMake 3.2 or higher
- On Linux and macOS, either `make` or `ninja` build utilities

CentOS에는 기본적으로 단 하나의 요구사항도 만족되어 있지 않습니다. 
추가적으로, git 관리와 파이썬 설치도 필요합니다. 
모두 설치해주도록 합니다. 

```dockerfile
RUN yum install -y gcc gcc-c++ make cmake git python3
```

이후에는 Arrow와 Miniforge 설정까지 합니다. 
PyArrow 2.0.0을 사용하기 때문에 Arrow 2.0.0을 받았습니다. 
셋째 줄은 Arrow를 빌드한 뒤 루트 디렉토리까지 돌아오는 명령어입니다. 

```dockerfile
RUN curl -O https://archive.apache.org/dist/arrow/arrow-2.0.0/apache-arrow-2.0.0.tar.gz
RUN tar -xf apache-arrow-2.0.0.tar.gz
RUN cd apache-arrow-2.0.0/cpp && mkdir release && cd release && cmake .. && make && cd && cd ..
RUN curl -L -O https://github.com/conda-forge/miniforge/releases/download/4.8.3-2/Miniforge3-4.8.3-2-Linux-x86_64.sh
```

그리고 추가적으로 로컬의 파일을 마운트할 수 있는 ``example``이라는 디렉토리를 하나 만들어 줬습니다. 

```dockerfile
RUN mkdir example
```

이것으로 Dockerfile은 마무리할 수 있겠습니다. 
완성된 Dockerfile은 다음과 같습니다. 

```dockerfile
FROM centos:latest

RUN cd /etc/yum.repos.d/
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
RUN cd ~/..
RUN yum update -y
RUN yum install -y gcc gcc-c++ make cmake git python3

RUN curl -O https://archive.apache.org/dist/arrow/arrow-2.0.0/apache-arrow-2.0.0.tar.gz
RUN tar -xf apache-arrow-2.0.0.tar.gz
RUN cd apache-arrow-2.0.0/cpp && mkdir release && cd release && cmake .. && make && cd && cd ..
RUN curl -L -O https://github.com/conda-forge/miniforge/releases/download/4.8.3-2/Miniforge3-4.8.3-2-Linux-x86_64.sh

RUN mkdir example
```

### Inside Docker

저는 다음 두 명령어로 Dockerfile을 빌드하고 컨테이너를 실행시켰습니다. 

```bash
$ docker build -t test:arrow .
$ docker container run --name arrow --volume /home/<username>/<mounting>/<directory>/:/example/ --rm -it test:arrow
```

컨테이너를 실행시키고 그 안에 들어왔으니, ``gcc``, ``cmake``, ``make``의 버전을 확인해 보겠습니다. 

```bash
$ gcc --version
$ cmake --version
$ make --version
```

저는 각각 8.5.0, 3.20.2, 4.2.1 버전이 나옵니다. 
값이 정상적으로 출력되면 된겁니다. 

다음으로 Miniforge를 설치하도록 하겠습니다. 
다음 명령어를 실행합니다. 

```bash
$ bash Miniforge3-4.8.3-2-Linux-x86_64.sh
```

이제 환경 변수 설정만 하면 Miniforge를 사용할 수 있습니다. 
다음 명령어를 실행합니다. 

```bash
$ vi ~/.bashrc
```

파일의 마지막에 다음과 같이 한 줄을 추가해 줍시다. 

```
export PATH=/root/miniforge3/bin:$PATH
```

파일을 저장한 뒤, 다음 명령어로 환경 변수를 설정해 줍니다. 

```bash
$ source ~/.bashrc
```

이제 ``conda``를 사용할 수 있게 되었습니다!
다음 명령어로 파이썬 3.6과 pyarrow 2.0.0 버전을 가지는 ITRC라는 환경을 만들 수 있습니다. 
실행하는 데에 시간이 한참 걸립니다. 

```bash
$ conda create -y -n ITRC -c conda-forge --file /apache-arrow-2.0.0/ci/conda_env_unix.yml --file /apache-arrow-2.0.0/ci/conda_env_cpp.yml --file /apache-arrow-2.0.0/ci/conda_env_python.yml --file /apache-arrow-2.0.0/ci/conda_env_gandiva.yml python=3.6 pyarrow==2.0.0
```

다음 명령어로 ``ITRC`` env를 실행시킬 수 있습니다. 

```bash
$ conda activate ITRC
```

가상 환경을 실행시킨 뒤, 그 안에서 PyFlink를 설치하면 됩니다. 

```bash
$ python -m pip install apache-flink==1.13.5
```

오류가 없었다면 참 좋았겠지만, 어김없이 오류가 발생합니다. 
버전 명세 없이 그냥 ``install apache-flink``를 실행해 보았지만 설치가 계속 실패했습니다. 
로그를 보니, 

```
SyntaxError: future feature annotations is not defined
```

라는 오류가 계속 발생하고, ``setuptools_scm``의 ``__init__.py``에서 일어나는 문제였습니다. 
해결방법은 간단했는데, 그냥 ``pip``에서 ``setuptools_scm``을 삭제해버렸더니 정상적으로 PyFlink 설치가 가능했습니다. 

마지막으로 코드 실행을 위해 Dockerfile에서 빼먹었던 자바 설치만 하면...

```bash
$ yum install -y java-11-openjdk-devel.x86_64
```

모든 것이 정상적으로 돌아갑니다!
마지막으로 현재 실행 중인 컨테이너를 이미지로 저장해 버립시다. 
현재 ``arrow``라는 이름으로 컨테이너가 실행되고 있고, itrc라는 이름과 1.0 태그를 가지는 이미지로 커밋하고 싶기 때문에 다음과 같은 명령어를 입력합니다. 

```bash
$ docker commit arrow itrc:1.0
```

# Future Work

앞으로 해야 할 일이 두 가지 있습니다. 

1. 용량: 
커밋한 이미지의 용량이 7.81GB로 거의 8GB에 육박합니다. 
이미지의 용량이 왜이렇게 커졌는지 지금은 이해하기 어려운데, CentOS 이미지부터 다시 만들어 보고 용량을 더 줄일 수 없을지 고민해야 할 것입니다. 

2. 네트워크 연결:
현재 제가 실험한 것은 edge 유닛의 데이터 생성 뿐입니다. 
이 edge 유닛을 도커 이미지로 만들어 배포/재생산하기 용이하게 만들었으니 *여러 컨테이너를 띄워서 동시에 실행시키는 것*과 *중앙 서버를 띄워서 데이터 스트리밍을 입수하는 것*, 이 두 가지를 수행 가능하게 만들어야 앞으로의 실험이 가능할 것입니다. 
관련된 링크는 [여기](https://www.tutorialworks.com/container-networking/)에 있습니다. 