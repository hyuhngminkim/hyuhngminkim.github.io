---
title: '[LevelDB #00] LevelDB Basics'
date: 2022-07-25
permalink: /posts/2022/07/leveldb-basics/
tags:
  - leveldb
---

출처: [leveldb-handbook](https://leveldb-handbook.readthedocs.io/zh/latest/)

LevelDB의 전체적인 구조에 대해 작성하는 포스팅입니다. 
원래는 시간이 걸리더라도 코드를 하나하나 살펴보면서 구조를 알아보려고 했습니다. 
공부를 위해서도 있지만, 공식문서가 워낙 빈약하고 불친절해서 어쩔 수 없었습니다. 
그런데 깃허브에서 잠깐 검색을 하고 있는데 LevelDB에 대한 문서가 있더군요. 
문제는 공식 문서가 아니라는 것과 중국어라는 것입니다. 
일단 구글 번역기의 도움을 받아 최대한 작성을 하겠지만, 아무래도 번역의 한계 때문에 잘못된 내용을 담을 수도 있습니다. 
이 부분은 유의하시길 바랍니다. 

# Table of Contents

- 전체 구조
- 읽기 및 쓰기 작업
- 로그(log)
- 메모리 데이터베이스
- sstable
- 캐시 시스템
- 블룸 필터
- 압축(compaction)
- 버전 관리


