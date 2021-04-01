---
layout: post
title:  "[JPA] 영속성 컨텍스트(Persistence Context) (2)"
date:   2021-04-01 13:00
category: jpa
icon: www
keywords: jpa, persistence context, persistence
image: 
preview: 0
---

### <u>flush</u>
앞에서 영속성 컨텍스트가 Entity 를 관리하는 방식에 대해서 살펴봤다.<br>

영속성 컨텍스트의 내용은 `flush` 가 발생하면서 DB에 해당 내용이 반영 된다.<br>
이때, flush 가 발생한다고 해서 영속성 컨텍스트의 내용이 비워지는 것은 아니다.<br>
단지 `영속성 컨텍스트의 변경사항을 DB와 동기화` 하는 것 뿐이다.<br>

영속성 컨텍스트를 flush 하는 방법은 아래와 같다.
- em.flush()
- 트랜젝션 커밋
- JPQL 쿼리 실행



### <u>영속성 컨텍스트의 이점</u>

영속성 컨텍스트를 사용함으로써 다음과 같은 이점을 얻을 수 있다.

1. 1차 캐시 지원에 따른 성능 향상<br>
Entity 조회 시 1차 캐시에 존재하는 경우 DB에 Select SQL 없이 바로 조회 가능하다.<br>
이를 통해 약간의 성능 향상을 기대할 수 있다.

2. 쓰기지연<br>
commit 이전까지는 관련 SQL 을 DB로 보내지 않기 때문에 대량 처리 등이 가능하다.

3. 변경감지(Dirty Checking)<br>
flush 관련...

4. 지연로딩(Lazy Loading)<br>

5. 동일성(Identity) 보장<br>
동일 ID의 Entity 를 반복 조회하는 경우, 1차 캐시에 존재하면 해당 Entity 를 리턴하고<br>
존재하지 않으면 최초 DB 조회 후 리턴하고, 해당 Entity 를 1차 캐시에 저장하기 때문에<br>
아래의 결과는 항상 `true` 가 된다.

```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");

System.out.println( a == b);
```