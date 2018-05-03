---
layout: post
title:  "postgresql - postgresql table migration query"
date:   2018-04-03 17:40:00 +0900
categories: SQL
---

# postgresql table migration
---------  

{% highlight sql %}
-- order에 price 컬럼이 새로 추가된다는 가정

-- 원본 테이블 복사
create table order_copy ( like order EXCLUDING CONSTRAINTS EXCLUDING INDEXES);

-- 카운트 체크
select count(*) from order_copy;  --Affected rows : 1196035

select count(*) from order; --1196035

-- 원복테이블 데이터 복사
INSERT INTO order_copy
(id, user_id, product_name, price)

	SELECT 
		id, user_id, product_name, price
	FROM (
		select 
			A.*
			, `계산` as price
		from order A

	)

-- 제약 사항 추가
alter table order_copy add primary key (id);
create index ix_order_01 on order_copy (id, user_id);

--원본 테이블
ALTER TABLE order RENAME TO order_back;

-마이그레이션 테이블
ALTER TABLE order_copy RENAME TO order;

{% endhighlight %}
