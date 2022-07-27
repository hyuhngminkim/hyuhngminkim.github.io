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
만약 이런 sstable이 무한정으로 생산되기만 한다면, 탐색을 할 때 선형 탐색을 해야 해서 굉장히 비효율적일 것입니다. 
따라서 LevelDB는 compaction이라는 압축 작업을 통해 "정기적으로" sstable 파일을 통합합니다. 

모둔 sstable 파일 자체의 내용은 변경할 수 없습니다. 
이것은 LevelDB의 디자인을 단순화하는 등 많은 이점을 가져옵니다. 

## manifest

LevelDB에는 **버전**이라는 개념이 있는데, 버전은 주로 각 레이어에 있는 모든 파일의 메타데이터를 기록하며, 메타데이터에는 (1) 파일 크기 (2) 최대 키 값 (3) 최소 키 값이 포함됩니다. 
버전 정보를 통해 각 파일의 최대/최소 키 값을 사용하여 데이터 검색 속도를 높이거나, compaction 제어를 위한 통계를 유지, 관리합니다. 

각 compaction이 완료되면 (즉, 각 sstable 파일이 추가되거나 제거되면) LevelDB는 새 버전을 만들고, 그 생성 규칙은 다음과 같습니다. 

$$ \text{versionNew} = \text{VersionOld} + \text{versionEdit} $$

versionEdit은 이전 버전을 기반으로 한 변경 사항을 나타냅니다. 

manifest 파일은 이러한 versionEdit 정보를 기록하는 데 사용됩니다. 
versionEdit 데이터는 레코드로 인코딩되어 manifest 파일에 기록됩니다. 
다음 그림은 3개의 versionEdit 레코드를 포함하는 파일의 개략도입니다. 
각 레코드에는 (1) 어떤 sst 파일이 추가되는지 (2) 어떤 sst 파일이 삭제되는지 (3) 현재 compaction의 첨자 (4) 로그 파일 번호 (5) 작업 seqNumber 및 기타 정보가 기록됩니다. 
이 정보를 통해 LevelDB는 시작할 때 빈 버전을 기반으로 이러한 레코드를 지속적으로 적용하고, 마지막 실행이 끝날 때 최종적으로 버전 정보를 얻을 수 있습니다. 

![](/images/leveldb/manifest.jpeg)
출처: *[https://leveldb-handbook.readthedocs.io/zh/latest/basic.html](https://leveldb-handbook.readthedocs.io/zh/latest/basic.html)*

## current

이 파일의 내용에는 현재 manifest 파일 이름을 기록하는 정보가 하나만 있습니다. 
LevelDB가 시작될 때마다 새로운 manifest 파일이 생성되기 때문입니다. 
따라서 데이터 디렉터리에 여러 manifest 파일이 있을 수 있습니다. 
current는 어떤 manifest 파일이 우리가 관심을 가지고 있는 파일인지 가리키는 데 사용됩니다. 