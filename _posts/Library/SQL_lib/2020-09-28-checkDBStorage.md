---
layout: post
title : "데이터베이스 용량 확인"
subtitle : "데이터베이스/테이블 용량 확인"
category: library
tags: lib-sql
---

# DataBase 할당된/사용중인 용량 체크

회사 데이터베이스에 시간이 지날 때마다 레코드가 무한히 쌓이는 구조의 테이블이 있다. (History를 저장해주는 등)

시간이 지날 수록 해당 테이블이 가지는 데이터가 많아 history를 읽는데 시간이 오래 걸리거나 (실제로 얼마나 차이나는지는 확실히는 모르지만..)

하드디스크 용량을 다량 차지하게 될 것이므로 이를 정리해줄 필요가 있다고 생각했다.

<br>

그래서 일단은 현재 데이터베이스가 얼마나 용량을 차지하고 있는지 확인해보고 싶었다.

여러가지 Query를 구글링해서 찾았는데 이를 저장하고자 한다.





## 1. 데이터베이스 Allocated/Used/Available 용량 확인

```sql
SELECT 
 b.groupname AS 'File Group'
 , Name
 , [Filename]
 , CONVERT (Decimal(15,2),ROUND(a.Size/128.000,2))  [Currently Allocated Space (MB)]
 , CONVERT (Decimal(15,2)
 , ROUND(FILEPROPERTY(a.Name,'SpaceUsed')/128.000,2)) AS [Space Used (MB)]
 , CONVERT (Decimal(15,2)
 , ROUND((a.Size-FILEPROPERTY(a.Name,'SpaceUsed'))/128.000,2)) AS [Available Space (MB)] 
FROM dbo.sysfiles a (NOLOCK) 
JOIN sysfilegroups b (NOLOCK) ON a.groupid = b.groupid 
ORDER BY b.groupname
```

위 Query를 통해 데이터베이스의 총 용량/사용 중 용량/ 사용 가능한 용량을 체크할 수 있다.

(로그에 사용되는 용량은 표시되지 않기 때문에 해당 정보로는 실제 데이터베이스의 용량을 알기 어렵다.)



ㅣ

## 2. 테이블 Row(행 갯수)/Total/Used/Unused 용량 확인

```sql
SELECT
    t.NAME AS TableName,
    s.Name AS SchemaName,
    p.rows AS RowCounts,
    SUM(a.total_pages) * 8 AS TotalSpaceKB,
    SUM(a.used_pages) * 8 AS UsedSpaceKB,
    (SUM(a.total_pages) - SUM(a.used_pages)) * 8 AS UnusedSpaceKB
FROM
    sys.tables t
INNER JOIN     
    sys.indexes i ON t.OBJECT_ID = i.object_id
INNER JOIN
    sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
INNER JOIN
    sys.allocation_units a ON p.partition_id = a.container_id
LEFT OUTER JOIN
    sys.schemas s ON t.schema_id = s.schema_id
WHERE
    t.NAME NOT LIKE 'dt%'
    AND t.is_ms_shipped = 0
    AND i.OBJECT_ID > 255
GROUP BY
    t.Name, s.Name, p.Rows
ORDER BY
    UsedSpaceKB desc
```

위 Query는  해당 데이터베이스의 모든 테이블에 대한 용량과 레코드 개수를 알려준다.

어떤 테이블이 많은 용량을 차지하고 있는지 확인하기 좋다.





## 3. 데이터베이스 총 용량 확인

```sql
CREATE TABLE #test (name varchar(100), db_size varchar(100),owner varchar(100),db_id int,created varchar(100),status varchar(1000),compatibility_level int)
 
INSERT #test 
EXEC sp_helpdb
 
SELECT name,db_size,owner,db_id,created,compatibility_level 
FROM #test
ORDER BY CONVERT(float,REPLACE(db_size,' MB','')) DESC

DROP TABLE #test 
```

위 Query를 사용하면 데이터베이스의 실질적인 용량을 표시해준다 (추가로 만들 날짜, OWNER, ID 등도 알 수 있음)

실제 용량을 가늠할때 사용할만 하지만, 아래 4번째가 더 유용하기 때문에 저걸 사용함





## 4. 데이터베이스 총 용량(DB+LOG) 확인

```sql
	;WITH DataBase_Size (SqlServerInstance,DatabaseName,DatabaseSize,LogSize,TotalSize)
AS
-- Define the CTE query.
(
  SELECT      @@SERVERNAME SqlServerInstance,
            db.name AS DatabaseName,
            SUM(CASE WHEN af.groupid = 0 THEN 0 ELSE af.size / 128.0E END) AS DatabaseSize,
            SUM(CASE WHEN af.groupid = 0 THEN af.size / 128.0E ELSE 0 END) AS LogSize,
            SUM(af.size / 128.0E) AS TotalSize
FROM        master..sysdatabases AS db
INNER JOIN  master..sysaltfiles AS af ON af.[dbid] = db.[dbid]
WHERE       db.name NOT IN ('distribution', 'Resource', 'master', 'tempdb', 'model', 'msdb') -- System databases
            AND db.name NOT IN ('Northwind', 'pubs', 'AdventureWorks', 'AdventureWorksDW')   -- Sample databases
GROUP BY    db.name 
)
-- Define the outer query referencing the  name.
SELECT *
FROM DataBase_Size order by DataBaseSize desc
```

이건 총 용량과 함께 DB SIZE와 LOG SIZE의 할당량도 확인할 수 있다.









## 마침

결론부터 얘기하자면 결국 총 용량을 확인해야하기 때문에 4번 Query를 주로 사용하게 된다.

테이블에 대한 자세한 정보는 2번을 이용하고, 할당되어 있는 DB SIZE 중에 실제로 어느정도 사용하고 있는지는 1번을 보면 되긴 한다.

<br>

사실 위 Query를 자기 입맛에 맞게 바꿔서 사용하면 되지만 그건 다음에 그럴 필요가 있을 때 해보도록 하겠다.