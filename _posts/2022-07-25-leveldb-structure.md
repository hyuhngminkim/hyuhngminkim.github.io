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
출처: *[https://leveldb-handbook.readthedocs.io/zh/latest/basic.html](https://leveldb-handbook.readthedocs.io/zh/latest/basic.html)*

LevelDB는 다음과 같은 구성 요소로 구성됩니다. 

- memtable
- immutable memtable
- log(journal)
- sstable
- manifest
- current

하나하나 살펴보도록 합시다. 

## memtable

LevelDB의 쓰기 작업은 데이터를 디스크 파일에 즉각적으로 플러시하지 않고 먼저 메모리 버퍼에 담습니다. 
memtable은 데이터 구성 및 메모리 유지/관리를 위한 구조입니다. 
memtable에는 입력된 모든 데이터가 **사용자가 정의한 정렬 방식**에 따라 정렬되어 저장되고, 저장된 데이터의 용량이 임계값(기본 4MB)에 도달하면 수정 불가능한 memtable (immutable memtable)로 변환됩니다. 
이와 동시에 사용자가 읽기 및 쓰기 작업을 계속 할 수 있도록 memtable이 생성됩니다. 
memtable의 맨 아래 계층은 [스킵 리스트](https://en.wikipedia.org/wiki/Skip_list)로 구성됩니다. 
스킵 리스트의 효율성은 이진 탐색 트리와 비슷하며, 대부분 작업의 시간 복잡도는 O(log n)입니다. 

## immutable memtable

memtable의 용량이 임계값에 도달하면 immutable memtable로 변환됩니다. 
둘의 구조는 동일하지만 immutable memtable은 읽기 전용이라는 차이점이 있습니다. 
immutable memtable이 생성되면 LevelDB의 백그라운드 압축 프로세스는 이를 통해 sstable을 만들고 디스크 스토리지에 유지합니다. 

## log

LevelDB의 쓰기 작업은 디스크에 직접 기록되는 것이 아니라 메모리에 먼저 기록됩니다. 
따라서 시스템이 다운되거나 하는 등의 상황이 발생하면 아직 기록되지 않고 메모리에 머무르는 데이터는 사라질 수 있습니다. 
이런 경우에 대비해 LevelDB는 메모리에 쓰기 전에 모든 쓰기 작업을 로그 파일에 씁니다. 

## sstable

디스크에 저장되는 LevelDB 데이터는 일부 메타데이터 파일을 제외하고 sstable을 통해 저장됩니다. 