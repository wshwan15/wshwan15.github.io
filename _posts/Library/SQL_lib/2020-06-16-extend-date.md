---
layout: post
title : "사물함 기간연장"
subtitle : "사물함 기간연장"
category: library
tags: lib-sql
---

# 사물함 기간 연장 (MSSQL)



###### 2020-08-19 변경사항

1) 날짜를 입력하여 해당 날짜 이전의 함만 대상으로 하는 조건 추가

2) 변경되는 함을 미리 Select 해볼 수 있는 B 타입 조건 추가

---





도서관 등 전자사물함 기간 연장해주는 Query.

입력할 부분에 있는 내용을 적고 실행하면 된다.

<br>

[사용법]

Type을 A로 둔 다음에그룹명과 지점명을 적고 리스트를 확인한 뒤에

연장할 기간을 입력한 뒤 Type을 C로 두어 실행하면 완료.

StartTime에 날짜를 넣으면 해당 날짜 이전에 시작된 함만 대상으로 하며, 공백 입력할 시 전체 대상.

(Type을 B로 두게 된다면 미리 변경 대상인 함들을 확인해볼 수 있음)

``` mssql
-- 전자사물함 기간 연장
DECLARE @Target_DBname varchar(40)
DECLARE @Target_GroupName varchar(40)
DECLARE @Target_AreaName varchar(40)
DECLARE @Target_AreaCode varchar(20)
DECLARE @Type varchar(2)
DECLARE @Target_Days varchar(10)
DECLARE @SQL varchar(300)
DECLARE @Start_Time varchar(100)


---------------------- 입력할 부분 -----------------------------
SET @Target_GroupName = '%원광%'; -- 그룹명
SET @Target_AreaName = '%%'; -- 지점명
SET @Target_Days = '0' -- 연장 기간
SET @Start_Time = '2020-05-13 23:59:59' -- 입력된 날짜 이전의 사용자만 대상으로 함, ''(공백) 입력할 시 전체 대상
SET @Type ='A'; 
-- A Type : 지점 셀렉트
-- B Type : 박스 셀렉트
-- C Type : 실 적용!!

----------------------------------------------------------------


IF(@Type = 'A')
BEGIN
	SELECT * FROM [SMTLocker_Mobile].[dbo].[tblAreaConfig] 
	WHERE GroupName like @Target_GroupName and AreaName like @Target_AreaName;
END
ELSE IF(@Type = 'B')
BEGIN
	DECLARE CUR CURSOR FOR -- CUR 커서 선언
	
	SELECT dbname, areacode FROM [SMTLocker_Mobile].[dbo].[tblAreaConfig] 
	WHERE GroupName like @Target_GroupName and AreaName like @Target_AreaName
	
	OPEN CUR
	FETCH NEXT FROM CUR INTO @Target_DBname, @Target_AreaCode
	
	WHILE @@FETCH_STATUS = 0
	BEGIN
		SET @Target_DBname += '.dbo.tblBoxMaster'
		
		IF(@Start_Time = '')
		BEGIN
			SET @SQL = 'select * from ' + @Target_DBname + ' where useState = 1 and areacode = ''' + @Target_AreaCode + '''';
		END
		ELSE
		BEGIN
			SET @SQL = 'select * from ' + @Target_DBname + ' where startTime <= ''' + @Start_Time + ''' and useState = 1 and areacode = ''' + @Target_AreaCode + '''';
		END
		
		EXEC(@SQL)
		
	FETCH NEXT FROM CUR INTO @Target_DBname, @Target_AreaCode
	END
	
	-- 커서 닫기
	CLOSE CUR
	DEALLOCATE CUR
END
ELSE IF(@Type = 'C')
BEGIN
	DECLARE CUR CURSOR FOR -- CUR 커서 선언
	
	SELECT dbname, areacode FROM [SMTLocker_Mobile].[dbo].[tblAreaConfig] 
	WHERE GroupName like @Target_GroupName and AreaName like @Target_AreaName
	
	OPEN CUR
	FETCH NEXT FROM CUR INTO @Target_DBname, @Target_AreaCode
	
	WHILE @@FETCH_STATUS = 0
	BEGIN
		SET @Target_DBname += '.dbo.tblBoxMaster'
		
		IF(@Start_Time = '')
		BEGIN
			SET @SQL = 'update ' + @Target_DBname + ' set endTime = DATEADD(D, ' + @Target_Days + ', endTime) where useState = 1 and areacode = ''' + @Target_AreaCode + '''';
		END
		ELSE
		BEGIN
			SET @SQL = 'update ' + @Target_DBname + ' set endTime = DATEADD(D, ' + @Target_Days + ', endTime) where startTime <= ''' + @Start_Time + ''' and useState = 1 and areacode = ''' + @Target_AreaCode + '''';
		END
		
		EXEC(@SQL)
		
	FETCH NEXT FROM CUR INTO @Target_DBname, @Target_AreaCode
	END
	
	-- 커서 닫기
	CLOSE CUR
	DEALLOCATE CUR
END




```



[리뷰]

이전에 한번 만들어봤던 쿼리지만 날려먹어서 다시 작성하게 됐는데 프로시저 부분은 하나도 기억이 나지 않아서 백지에서 시작하는 느낌이었다.

그래도 다른 구조적인 부분이나 `CURSOR` 같은 건 쉽게 기억이 나서 요거 짜는데 1시간 좀 더 걸렸던 것 같다.. SQL 초보의 숙명..

아무튼 다시 복구해서 다행~ 프로시저는 앞으로도 유용하게 사용할 것 같다.