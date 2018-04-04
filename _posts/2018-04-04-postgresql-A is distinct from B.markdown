---
layout: post
title:  "postgresql - A is distinct from B"
date:   2018-04-04 11:32:00 +0900
categories: postgresql
---

# postgresql - A is distinct from B

```sql
-- 테스트 데이터 테이블
with a as (
	select 1 as id, 1 as index
	union all
	select 2 as id, 2 as index
	union all
	select 3 as id, 3 as index
)
,b as (
	select 1 as id, 1 as index, 'Y' as del_flag
	union all
	select 1 as id, 1 as index, 'N' as del_flag
	union ALL
	select 2 as id, 1 as index, 'N' as del_flag
)
```

```sql
--처음 쿼리 작성
select 
	a.id
	,count(b.id)
from a left join b on a.id = b.id
where b.del_flag = 'N'
group by a.id;
```

# 원했던 결과  
- 원했던 결과는 a 테이블에 id에 따른 카운트였다. 그래서 left outer join을 걸었다.

 | id | count |
 |:------ |:-------:|
 | 1 | 1 |
 | 2 | 1 |
 | 3 | 0 |

# 실제 결과  

 | id | count |
 |:------ |:-------:|
 | 1 | 2 |


- `where b.del_flag = 'Y'` 이조건 때문인데  equal(=) 비교는
`null = 'Y'` 의 결과는 false 이기 때문이다.

```sql
--수정 쿼리
select 
	a.id
	,count(b.id)
from a left join b on a.id = b.id
where b.del_flag IS DISTINCT FROM 'Y'
group by a.id
```

처음에는 `AND (b.del_flag = 'N' OR b.del_flag is null)`를 사용하여 결과 값을 뽑아 냈는데 null값또한 체크 해줄 수 있는 구문이 있을것 같아 찾아보았더니 `IS DISTINCT FROM ` 발견

수정 쿼리에서 `IS DISTINCT FROM ` 구문을 활용해 Y를 제외한 N값과 null값 또한 포함 하여 원하던 결과값 출력!