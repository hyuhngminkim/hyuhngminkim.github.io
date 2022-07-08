---
title: 'Trying PyFlink'
date: 2022-07-08
permalink: /posts/2022/07/trying_pyflink/
tags:
  - flink
---

[지난 포스팅](https://hyuhngminkim.github.io/posts/2022/07/trying_docker/)에서는 도커를 설정하는 과정을 다뤘습니다. 
처음으로 Dockerfile을 만들고, 베이스 이미지로 사용한 CentOS 위에 자바나 파이썬, 플링크같이 이것저것 올리는 과정을 기록했습니다. 
하지만 플링크를 어떻게 다루는지보다는 도커 설정과 실행시키는 것에 초점을 뒀기 때문에 그 이미지 그대로 도커 컨테이너를 실행시킨다면 제대로 작동하지 않습니다. 
이번 포스팅에는 플링크의 파이썬 API 문서를 살펴보며 어떻게 환경 설정을 하고 실행시키는 것인지 알아보도록 하겠습니다. 

# References

[Apache Flink Python API Documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/docs/dev/python/overview/)

# Prerequisites

기본적으로 실행하기 위해 필요한 요구사항은 다음과 같습니다. 

- Java 11
- Python 3.6, 3.7, or 3.8

모두 지난 포스팅에서 만든 이미지에 포함된 요구사항이기 때문에 신경 쓸 필요가 없습니다!
사실 이 요구사항을 보고 만든 이미지이기 때문에 당연한 것이긴 합니다. 
그리고 다음 코드를 통해 PyFlink를 설치합니다. 
마찬가지로, 이 역시 Dockerfile에 명시해서 우리가 만든 이미지에는 포함되어 있습니다. 

```bash
$ python -m pip install apache-flink
```

그런데 이렇게 하니까 이런 오류가 계속 나타납니다. 

```
The flink-python jar is not found in the opt folder of the FLINK_HOME: /usr/local/lib64/python3.6/site-packages/pyflink
```


