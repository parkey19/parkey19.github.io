---
layout: post
title:  "postgresql - session lock check"
date:   2018-04-09 18:32:00 +0900
categories: postgresql
---

# postgresql - session lock check query

# 현재 모든 session 실행 중인 쿼리 확인  
```sql
--session query stat
select * from pg_stat_activity; 
```

```sql
-- lock table 검색
SELECT t.relname,
       l.locktype,
       page,
       virtualtransaction,
       pid,
       mode,
       granted
FROM pg_locks l,
     pg_stat_all_tables t
WHERE l.relation = t.relid
ORDER BY relation ASC;
```

# 프로세스 kill

```sql
--해당 pid만 kill 
select pg_cancel_backend(4678249); 
```

```sql
--상위 pid 또한 모두 kill 명령
SELECT pg_terminate_backend(32519) FROM pg_stat_activity ; 
```

------

# 참조 링크  
[PostgreSQL DBMS 관리를 위한 Query 몇가지](http://blog.naver.com/PostView.nhn?blogId=admass&logNo=220900485272&parentCategoryNo=&categoryNo=9&viewDate=&isShowPopularPosts=true&from=search)
