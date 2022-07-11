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

## Using [PyPI](https://pypi.org/project/apache-flink/)? (Failed)

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

## Using Docker? (Failed)

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

