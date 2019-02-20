---
layout: post
title:  "elasticsearch - create index"
date:   2018-05-14 22:40:00 +0900
categories: java
---

# elasticsearch 인덱스 생성

---------  

```
- customer 인덱스 생성
curl -XPUT 'localhost:9200/customer?pretty'

- customer 인덱스에 추가
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d'
{
  "name": "John Doe"
}'

- 조회
curl -XGET 'localhost:9200/customer/external/1?pretty'

- 인덱스 삭제
curl -XDELETE 'localhost:9200/customer?pretty'

-인덱스 조회
curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```

## 참조 링크
[참조](https://iju707.gitbooks.io/elasticsearch/content/_delete_an_index.html)