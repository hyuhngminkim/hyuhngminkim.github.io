---
title: '[LevelDB #01] LevelDB Structure'
date: 2022-07-25
permalink: /posts/2022/07/leveldb-structure/
tags:
  - leveldb
---

LevelDB는 LSM 트리를 사용한 대표적인 데이터베이스입니다. 
(LSM 트리에 대해서는 제 이전 [포스팅](https://hyuhngminkim.github.io/posts/2022/07/lsm-tree/)이나 [위키피디아](https://en.wikipedia.org/wiki/Log-structured_merge-tree)를 참고하시길 바랍니다.)
LSM 트리를 사용하기 때문에 LevelDB는 효율적인 쓰기 성능과 상대적으로 덜 효율적인 읽기 성능을 보입니다. 
LevelDB가 쓰기에서 뛰어난 성능을 보이는 이유는 쓰기 작업이 들어오면 그 작업을 스토리지에 즉각적으로 반영하는 것이 아니라 메모리에 위치하는 버퍼에 저장만 한 뒤, 버퍼가 가득 차면 스토리지로 내려보내는 방식으로 작동하기 때문입니다. 
따라서 쓰기 작업이 있을 때마다 스토리지를 참조할 필요가 없어지기 때문에 쓰기 성능이 좋아지겠죠. 

# 전체 구조

![](/images/leveldb/leveldb_arch.jpeg)
*https://leveldb-handbook.readthedocs.io/zh/latest/basic.html*

