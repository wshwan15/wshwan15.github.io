---
layout: post
title : "중복된 개체 제거"
subtitle : "중복된 개체 제거"
category: library
tags: lib-sql(MSSQL)
---

# 중복된 개체를 제거하고 가장 최신의 것만 남기기

HOHOLOCK 중복 유닛코드를 제거하는 Query.

2개의 Select 문과 2개의 커서로 구성되어 있다.

<br>

[사용법]

사실 앞으로 쓸 일은 없는 Query 지만, 기록해두는 용도로..

``` mssql
-- 변수 선언

DECLARE @Target_UserCode varchar(100)
DECLARE @Target_UserCodeSize int

DECLARE @sub_count int

DECLARE @Target_BoxNo varchar(20)
DECLARE @Target_UserCode_sub varchar(100)
DECLARE @Target_StartTime DateTime
DECLARE @Target_AreaCode varchar(50)

-- 로직

DECLARE CUR CURSOR FOR -- CUR 커서 선언  

SELECT usercode, count(userCode)as count
  FROM [HOHO_LOCK].[dbo].[tblBoxMaster]
  where useState = 1 and serviceType = 1 and userCode != ''
  group by userCode
  having count(usercode) > 1;
  
OPEN CUR
FETCH NEXT FROM CUR INTO @Target_UserCode, @Target_UserCodeSize

WHILE @@FETCH_STATUS = 0 -- 조건에 맞는 전체 UserCode 대상
BEGIN
	SET @sub_count = @Target_UserCodeSize;
	
	DECLARE CUR_sub CURSOR FOR -- CUR_sub 커서 선언  
	
		SELECT areacode,boxno, usercode, startTime
		FROM [HOHO_LOCK].[dbo].[tblBoxMaster]
		where usercode = @Target_UserCode and useState = 1 and serviceType = 1
		order by starttime desc;
		
	OPEN CUR_sub
	FETCH NEXT FROM CUR_sub INTO @Target_AreaCode, @Target_BoxNo, @Target_UserCode_sub, @Target_StartTime
	
	WHILE @@FETCH_STATUS = 0 -- 각 UserCode 마다 처리
	BEGIN
		
		IF(@sub_count = @Target_UserCodeSize)
		BEGIN
			print '(' + CONVERT(varchar,@Target_UserCodeSize) + '/' + CONVERT(varchar,@sub_count) + ') ' + @Target_AreaCode + ' / ' + @Target_UserCode_sub+ ' / ' + @Target_BoxNo  + ' / ' + CONVERT(varchar,@Target_StartTime) + '  : 보존' 
		END
		ELSE
		BEGIN
			print '(' + CONVERT(varchar,@Target_UserCodeSize) + '/' + CONVERT(varchar,@sub_count) + ') ' + @Target_AreaCode + ' / ' + @Target_UserCode_sub+ ' / ' + @Target_BoxNo  + ' / ' + CONVERT(varchar,@Target_StartTime) + '  : 제거'
			Update [HOHO_LOCK].[dbo].[tblBoxMaster] set useState=2, userCode='', userName='', userPhone='', dong='', addressNum='', transCode='', transPhone='', barCode='', deliveryType=0, boxPassword='', paycode='', payAmount=0, useTimeType=0, startTime=GETDATE(),endTime=GETDATE(), UserCardNo='', updateTime=GETDATE(), DeptGroupCode=-1 where areacode = @Target_AreaCode and boxno = @Target_BoxNo and usercode = @Target_UserCode_sub;

			
		END
		SET @sub_count = @sub_count - 1
	
	FETCH NEXT FROM CUR_sub INTO @Target_AreaCode, @Target_BoxNo, @Target_UserCode_sub, @Target_StartTime
	END
	
	-- 서브 커서 닫기
	CLOSE CUR_sub
	DEALLOCATE CUR_sub
	
FETCH NEXT FROM CUR INTO @Target_UserCode, @Target_UserCodeSize
END

-- 커서 닫기
CLOSE CUR
DEALLOCATE CUR

```



[리뷰]

`CURSOR` 를 중첩해서 사용했던 것 이외에는 특별했던 점은 없었다.

SQL은 뭔가 코드의 앞뒤를 분석하기가 힘든게 단점인 것 같다 (아니면 그저 나의 무지일지도)