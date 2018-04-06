---
layout: post
title:  "postgresql - merge 기능 upsert"
date:   2018-04-04 11:32:00 +0900
categories: postgresql
---

# postgresql - merge 기능 upsert

oracle merge 문에 대응 되는 postgresql에서 upsert 구문

```sql
INSERT INTO table_name(column_list) VALUES(value_list)
ON CONFLICT target action;
```

# target  
---
	1. (column_name) 컬럼 명
	- ex) ON CONFLICT (id)
	2. ON CONSTRAINT constraint_name (제약 이름)
    - ex) ON CONFLICT (table_index_key_name)
	3. WHERE predicate
	- ex) ON CONFLICT (name) WHERE condition = true DO NOTHING

# action
---
	1. DO NOTHING  
	2. DO UPDATE SET ~ WHERE 조건

# 예제
---  
```sql

--테이블 생성
CREATE TABLE customers (
 customer_id serial PRIMARY KEY,
 name VARCHAR UNIQUE,
 email VARCHAR NOT NULL,
 active bool NOT NULL DEFAULT TRUE
);

--테스트 데이터 생성
INSERT INTO customers (NAME, email)
VALUES
 ('IBM', 'contact@ibm.com'),
 (
 'Microsoft',
 'contact@microsoft.com'
 ),
 (
 'Intel',
 'contact@intel.com'
 );

--테이블 명세
CREATE TABLE "public"."customers" (
	"customer_id" int4 NOT NULL DEFAULT nextval('customers_customer_id_seq'::regclass),
	"name" varchar COLLATE "default",
	"email" varchar NOT NULL COLLATE "default",
	"active" bool NOT NULL DEFAULT true,
	CONSTRAINT "customers_pkey" PRIMARY KEY ("customer_id") NOT DEFERRABLE INITIALLY IMMEDIATE,
	CONSTRAINT "customers_name_key" UNIQUE ("name") NOT DEFERRABLE INITIALLY IMMEDIATE
)
WITH (OIDS=FALSE);  
ALTER TABLE "public"."customers" OWNER TO "postgres";
CREATE UNIQUE INDEX  "customers_name_key" ON "public"."customers" USING btree("name" COLLATE "default" "pg_catalog"."text_ops" ASC NULLS LAST);  
CREATE INDEX  "customers_pkey" ON "public"."customers" USING btree(customer_id "pg_catalog"."int4_ops" ASC NULLS LAST);

-- target 2 예
INSERT INTO customers (NAME, email)
VALUES
 (
 'google',
 'hotline@microsoft.com'
 ) 
ON CONFLICT ON CONSTRAINT customers_name_key 
DO NOTHING;

-- target 1 예
INSERT INTO customers (name, email)
VALUES
 (
 'Microsoft',
 'hotline@microsoft.com'
 ) 
ON CONFLICT (name) 
DO NOTHING;

-- target 1 예
INSERT INTO customers (name, email)
VALUES
 (
 'Microsoft',
 'hotline@microsoft.com'
 ) 
ON CONFLICT (name) 
DO
 UPDATE
   SET email = EXCLUDED.email || ';' || customers.email;

-- target 3 예
INSERT INTO customers (name, email)
VALUES
 (
 'Intel',
 'contact@intel.com'
 ) 
ON CONFLICT (name) WHERE active = true
DO
 UPDATE
   SET email = EXCLUDED.email || ';' || customers.email;
```


# 참조 링크  
[postgresql tutorial](http://www.postgresqltutorial.com/postgresql-upsert/)
