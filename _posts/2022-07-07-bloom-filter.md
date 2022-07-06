---
title: 'Bloom Filter'
date: 2022-07-07
permalink: /posts/2022/07/bloom-filter/
tags:
  - database
  - index
---

블룸 필터(bloom filter)는 정말 단순하면서도 강력한 성능을 보이기 때문에 수많은 데이터베이스에서 사용되는 자료구조입니다. 
사실 카테고리를 인덱스라고 설정하긴 했는데, 엄밀히 말하면 인덱스라고 보기에는 어렵습니다. 
데이터가 담긴 위치를 정확하게, 혹은 근사해서라도 저장하고 있지 않기 때문입니다. 
오히려 인덱싱 혹은 탐색을 조금 더 쉽게 하기 위한 조미료라고 생각하면 편합니다. 
이번 포스팅에서는 블룸 필터에 대해서 살펴보도록 하겠습니다. 

# Bloom Filter

출처: *Bloom, Burton H. "Space/time trade-offs in hash coding with allowable errors." Communications of the ACM 13.7 (1970): 422-426.*


*To be updated...*