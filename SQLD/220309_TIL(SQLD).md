# 데이터 존재시 열 삭제 및 추가

### 데이터 존재시 열 삭제

- 데이터 있건 없건 모두 삭제 가능
- 주의 / 삭제된 컬럼은 복구 불가



### 기존 테이블에 열 추가

- 열 삭제와 달리 열 추가엔 주의사항이 있다.

  1) 기존 테이블에 데이터가 있으면 고려할 게 많다
  2) NOT NULL 속성을 추가할 수 없다.
     - 이미 기존 레코드가 있음, 당연히 새 열을 추가하면 빈 열로 추가되는 것
  3) NULL 값으로 채울 수도 없다.

- #### 기존 테이블에 데이터가 있는 경우

  - DEFAULT 제약과 IDENTITY 속성을 사용하여 추가한다.
  - Default는 열에 기본 값을 저장해주므로 `DEFAULT('') NOT NULL` 
    - NOT NULL 속성을 추가할 순 없었지만 디폴트로 공백값을 넣어준 것
  - Identity 속성은 자동 증가값을 저장해주므로 `IDENTITY(1,1) NOT NULL`
    - (1,1)은 초기값, 증가값 1, 2, 3 ..

  > DEFAULT나 IDENTITY나 열에 어떤 값을 넣어준다는 점에서 비슷함

- ### 열 추가시 default 지정

```sql
ALTER TABLE dbo.memberTable
	ADD		mem_point		int		default('0')
```

- ### 열 추가시 Identity 지정

```sql
ALTER TABLE dbo.memberTable
  ADD  mem_point  int  IDENTITY(1,1) NOT NULL
  
표 전체 보기
SELECT *
  FROM memberTable
```



```sql
CREATE TABLE sql_server_test_a 
( 
    ID         NVARCHAR(4000), 
    FIRST_NAME NVARCHAR(200), 
    LAST_NAME  NVARCHAR(200) 
); 

INSERT INTO sql_server_test_a (ID, FIRST_NAME, LAST_NAME) VALUES ('1', 'Paris', 'Hilton'); 

INSERT INTO sql_server_test_a (ID, FIRST_NAME, LAST_NAME) VALUES ('2', 'Nicky', 'Hilton');
```



## 동시에 열 추가 하기

```sql
ALTER TABLE dbo.memberTbl
	ADD mem_point int NULL
ALTER TABLE dbo.memberTbl
	ADD mem_point2 int NULL
ALTER TABLE dbo.memberTbl
	ADD mem_point3 int NULL
```

- 위의 반복 작업을 한번에 하는 방법은 없을까?

```sql
ALTER TABLE dbo.memberTbl ADD
	mem_point 	int 	NULL,
	mem_point2 	int 	NULL,
	mem_point3 	int 	NULL;
```

- ADD 한번만 쓰면 된다, 괄호 쓰면 에러남

```sql
ALTER TABLE 테이블이름
	ADD
		mem_point 	int 	NULL,
		mem_point2 	int 	NULL,
		mem_point3 	int 	NULL;
```

> 요렇게 쓰는 방법도 있음



- ### 동시에 열삭제

```sql
ALTER TABLE 테이블
	DROP COLUMN
		mem_point
		mem_point2
```



## RENAME COLUMN

- 컬럼명 수정 기능 - 안하는게 좋음
- 사용법(SQL Server 기준, 오라클은 다를 수 있다)
- `EXEC sp_rename 'dbo.memberTbl.mem_point4', 'mem_point7';`
  - EXEC는 execute 실행해라 / sp_rename을 / 기존 컬럼명 / 새 컬럼명



#### sp_rename 이란?

- `EXEC` 는 저장 프로시저를 호출할 때 사용
- 현재 db내의 개체의 이름을변경할 수 있음
  - 테이블이름, 인덱스이름, 열이름, 별칭 데이터 형식 등
- 변경할게 많으면 삭제후 새로 만드는 게 더 편할 수 있음
- 열 변경시, 테이블 이름 변경 시 마지막 옵션은 생략 가능하다.
  - 열 변경시 (COLUMN 생략가능)
    - `EXEC sp_rename 'dbo.memberTbl.mem_point3', 'mem_point4', 'COLUMN'`
  - 테이블 변경시 (OBJECT 생략가능)
    - `EXEC sp_rename 'dbo.memberTbl', 'memberTable', 'OBJECT'`



## 종속성

##### 테이블 삭제나 변경 시, 이 개체가 다른 테이블에 종속되어 있는지 여부를 체크할 것

- RDBMS - 관계형 데이터베이스 관리시스템
- 테이블 우클릭, 종속성 보기



## 종속성 Reporting

- ##### 테이블 종속성 보고

  - `EXEC sp_depends @objname = '알고싶은 테이블이름';`

- MSSQL Server 이후 버전에서는 기능 제거 예정
  - `sys.dm_sql_referencing_entities`
  - `sys.dm_sql_referenced_entities`로 대체할 것 권장

##### 개체 종속성 식별

- 요청 또는 구조 문제로 스키마가 변경돼야 할 때, 변경할 스키마의 종속 개체들을 확인할 때 사용
  - `sys.dm_sql_referencing_entities ('이름','형식')` (dm = Dynamic Management Functions)

```sql
USE Pusbs;  사용할 데이터베이스 선택
GO
SELECT * FROM sys.dm_sql_referencing_entities ('스키마.테이블개체명','OBJECT');
GO
```



## 종속성, INNER JOIN

```sql
USE Pubs;
GO
SELECT * FROM sys.objects;
```

> 이용 DB 내의 모든 개체를 출력
>
> > DB내 개체는 테이블 뿐 아니라 트리거, 뷰, 저장 프로시져 등 다양한 종류가 포함되어 있다.

- 오브젝트 중에서 특정 조건에 맞는 것만 뽑고 싶을 때 INNER JOIN

```sql
SELECT name,type,type_desc
  FROM sys.objects WHERE name='titleview'
```

- WHERE은 파이썬의 if문과 비슷해보인다

  ```python
  a = [name,type,type_desc for name,type,desc in sys.objects if name=='titleview']
  print(a)
  ```

  

```sql
SELECT a,b,c FROM sys.dm_sql_referencingentities('개체명','타입');
SELECT name,type, c_id FROM sys.objects;

두 조건 합치기 INNER JOIN
SELECT a,b,c,name,type,c
  FROM sys.dm_sql.referecing_entities ('개체명','타입') INNER JOIN sys.objects
  		ON c = c_id;
```

1번 쿼리에서 조건에 맞는 레코드들 + 2번 쿼리에서 조건에 맞는 레코드들 (INNER JOIN은 + 효과)

ON을 통해서 그 중 보고 싶은 레코드의 조건 지정

> 참고 : INNER JOIN은 테이블을 합치는 연산이다.





# Datatype Alteration 데이터 타입 변경

```sql
ALTER TABLE dbo.memberTbl
  ALTER COLUMN mem_point smallint;
```

> mem_point의 데이터 타입을 smallint로 바꾼다

## 테이블 구조 확인 Table Structure

```sql
EXEC sp_columns 테이블명;
또는
EXEC sp_help 테이블명;
```

- EXEC sp_columns
  - Table_qualifier 소유 DB
  - Table_owner 소유 스키마
  - Table_name 테이블 이름
  - Column_name 컬럼 이름
  - Data_type, Type_name 타입 정보
- EXEC sp_help
  - 좀 더 광범위한 정보 제공

```sql
SELECT * FROM sys.columns;
			또는 sys.tables;
			
			뒤에 ORDER BY 컬럼명 ASC; 을 쓰면 컬럼 기준 정렬 (DESC 디센딩-내림차순)
```

> SELECT를 통해 정보 보는 법 / DB안에 있는 모든 컬럼이 나옴... 그럴땐 조건 걸기



### 뷰를 통해 보는 방법

information_schema.columns  사용

	- 뷰
	- 시스템 뷰 에서 확인 가능 - 뷰는 조회용 가상 테이블이다.



# UPDATE

##### 데이터를 조작, 변경하는 쿼리문

##### 구조

​	`UPDATE [테이블명] SET [컬럼명] = 값 (추가)[, 컬럼 = 값, 컬럼=값..] (추가)[WHERE 조건];`

> UPDATE, DELETE 등 데이터를 직접적으로 변경하는 쿼리는 조심해야함
>
> WHERE 조건 절은 생략 가능하나 생략하면 모든 행의 값이 수정 됨
>
> 값 대신 DEFAULT를 적으면 테이블에 정의된 DEFAULT 값으로 변경됨

- 예)

- ```sql 
  UPDATE dbo.memberTbl SET mem_lebel='EXPERT', mem_point=7000 WHERE mem_id ='kim';
  ```



##### 번외

​	테이블 생성 때 값을 바로 넣는 경우엔

`INSERT INTO 테이블명 (컬럼명,컬럼명) VALUES ('값','문자열',숫자);`



## NULL 값 UPDATE

- 기존 레코드들의 NULL값을 한꺼번에 변경하고자 할때
- `UPDATE 테이블명 SET 컬럼명 = ISNULL(컬럼명, '변경할 값');`
  - null인 레코드만 해당하는 sql 내장함수 ISNULL



## UPPER(),LOWER()

> 대소문자 변경 함수

## INITCAP()

> 영문 앞글자만 대문자로 바꿔주는 함수 Initial Capital = 앞글자 대문자
>
> korea seoul -> Korea Seoul
>
> > 오라클에는 있으나 SQL Server에는 없다 > 사용자 정의함수로 만들어서 쓰는 경우가 많음



## ISNULL 실습

`SELECT ISNULL(컬럼명, '값없음') AS 컬럼별칭 FROM 테이블명;`

## SQL 자동완성기능

주피터는 shift tab or tab

Ctrl + Enter



# 데이터 베이스 생성부터 삭제

참고 - 파이썬 주석은 #지만 sql 주석은 --

```sql
-- 쿼리 설명(한줄짜리)
/*
여러줄 쓰는 경우는 이렇게
*/
```



#### AAA라는 데이터 베이스를 생성

```sql
CREATE DATABASE AAA;
```

#### AAA라는 데이터베이스에서 작업

```sql
USE AAA;
```

#### TABLE_A 생성

```sql
 CREATE TABLE TABLE_A (ID varchar(16) NULL, AGE tinyint NULL)
```

#### TABLE_A 자료입력

```sql
-- 주석작성
INSERT INTO dbo.TABLE_A VALUES ('jack',16);
```

#### TABLE_A 자료 출력

```sql
SELECT ID AS 아이디, AGE AS 나이 FROM dbo.TABLE_A
```

#### TABLE_A 삭제

```sql
DROP TABLE dbo.TABLE_A
```

#### AAA 데이터베이스 삭제

```sql
DROP DATABASE AAA; --ERROR뜬다. 현재 사용공간이 AAA이기 때문
```

#### 사용 DB영역을 MASTER로 변경

```sql
USE MASTER;
```

#### 다시 AAA 데이터베이스 삭제

```sql
DROP DATABASE AAA;
```



# 유니코드 형식 데이터 입력

```sql
CREATE DATABASE TestDB;
GO

USE TestDB;
CREATE TABLE dbo.testTB(
	mem_num			smallint		NOT NULL,
    mem_nation		nvarchar(25)	NOT NULL,
    mem_birth		date			NOT NULL,
    mem_income		money			NOT NULL
)

-- 데이터 삽입
INSERT TestDB.dbo.testTB
VALUES
	(1, N'한국', '1996-07-27', 3000)
	(2, N'America', '1996-07-27', 50000)
	(3, N'대충중국어', '1996-07-27', 123)
	(4, N'일본어', '1996-07-27', 1209);

-- 유니코드식 문자열을 입력할 땐 N'문자'로 써야한다

SELECT * FROM TestDB.dbo.testTB;
```



# CONSTRAINT 

- 열의 제약조건
- DROP 할때 DROP CONSTRAINT 하면 열의 제약조건만 삭제 할 수 있다.
- 쿼리 또는 개체탐색기를 통해 만들 수 있다.
- DB 생성, 테이블 생성,제약조건 입력, 자료입력, 출력, DB삭제 를 반복연습 할 것
  - 이 과정에서 여러 함수도 써보면 좋다

##### 제약을 걸 때 이해하고 넘어가야 할 것

- IDENTITY
- PRIMARY KEY
- DEFAULT

##### 예시

```sql
CREATE TABLE dbo.testTB (
	COL1 		INT				IDENTITY(100,1)		 PRIMARY KEY,
    COL2		NVARCHAR(20)	DEFAULT N'없음'		NOT NULL
    COL3		DATE			DEFAULT GETDATE() 	-- 현재시각 함수
)
```





# 함수 사용

## SELECT

1. SECECT * FROM 테이블
    - 테이블 내 모든 열 출력
2. SELECT 컬럼1, 컬럼2, 컬럼3, LEFT(컬럼4, 8) AS '시간' FROM 테이블;
    - LEFT(컬럼, 8) 왼쪽부터 8글자만 출력
3. CONVERT(변환시킬 타입, 변경대상, 변경하려는 형식)
    - SELECT CONVERT(varchar(8), GETDATE(), 108);  -- 108은 시,분,초로 나타내라는 코드



## CONSTRAINT 예제2

- IDENTITY
- PRIMARY KEY
- DEFAULT
- **UNIQUE**
    - primary key가 이미 있을 때 중복허용 안하는 제약조건

##### <표 생성>

```sql
CREATE TABLE dbo.employeeTB(
	mem_idx		INT			 IDENTITY		PRIMARY KEY,
	mem_id		CHAR(16)	 UNIQUE			NOT NULL,
	mem_name	nvarchar(16)				NOT NULL,
	mem_date	DATE						NULL,
	mem_end		DATE						NULL,
	mem_reason	nvarchar(50)				DEFAULT N'일신상의 사유'
)
```

##### <값 입력>

```sql
INSERT INTO dbo.employeeTB
	VALUES
		('fireman','파이어','2010-12-12','2020-12-12','해외'),
		('women','요자','2012-12-12','2019-12-12',DEFAULT);
```

또는

```sql
INSERT INTO dbo.employeeTB(
	mem_id,
    mem_name,
    mem_date,
    mem_end,
    mem_reason
)
VALUES
	('fireman','파이어','2010-12-12','2020-12-12','해외')
	('women','요자','2012-12-12','2019-12-12',DEFAULT);
```



## IDENTITY 값의 공백

- 입력실패 또는 레코드 삭제시 IDENTITY 열의 값에 공백이 생길 수 있다

- 간격을 없애려면 SET IDENTITY_INSERT 를 ON으로 설정
    - `SET IDENTITY_INSERT 테이블명 ON`
    - 하고 identity 값을 직접 입력해서 새로운 레코드 삽입

```sql
SET IDENTITY_INSERT employeeTB ON;

INSERT INTO dbo.employeeTB (mem_idx,mem_id,mem_name,mem_date,mem_reason)
VALUES (2,'womeen','요지','2012-12-12',DEFAULT);

SET IDENTITY_INSERT employeeTB OFF;
```





# SELECT 쿼리의 기본 종합

1. 기본형태

    ```sql
    SELECT 컬럼1,컬럼2 FROM 테이블
    -- 또는
    SELECT * FROM 테이블 -- 전체출력 
    ```

2. WHERE 조건절

    ```sql
    SELECT * FROM 테이블 WHERE 컬럼=값;
    -- 테이블에서 컬럼=값과 같은 부분의 모든 컬럼을 출력
    ```

3. 검색할 열 정렬

    ```sql
    SELECT * FROM 테이블 ORDER BY 컬럼 DESC; -- ASC 오름=기본값, DESC 내림
    
    SELECT * FROM 테이블 ORDER BY 컬럼 DESC, 컬럼2;
    -- 컬럼에 대해 내림차순 하되 같은 값일땐 컬럼2 오름차순으로 정렬
    ```

4. 검색할 열 순서 지정

    ```sql
    SELECT 컬럼4, 컬럼2 FROM 테이블;
    ```

5. 별칭사용 AS

    ```sql
    SELECT col1 AS 컬럼1, col2 AS 컬럼2 FROM 테이블
    -- AS는 생략 가능
    SELECT col1 컬럼1 FROM 테이블
    ```

6. 새 열 추가(원래 있던 열처럼 사용)

    ```sql
    SELECT '판매수량' AS 새열, lowqty FROM dbo.discounts
    ```

    > ![image-20220309211758429](220309_TIL(SQLD).assets/image-20220309211758429.png) 



7. 중복데이터 걸러내기

    - 컬럼에 중복값이 여러개 있을 땐 DISTINCT (구별점)사용

    ```sql
    SELECT 컬럼 FROM 테이블 -- 컬럼 값 전부 출력
    SELECT DISTINCT 컬럼1 FROM 테이블 ORDER BY 컬럼1 -- 고유값만 출력
    ```

    

8. 상위 n개 레코드만 가져오기 TOP n

    ```sql
    SELECT TOP 5 * FROM 테이블
    ```

    - #### 문제점

        - n개항 뒤로 같은 값을 가지는 행들은 잘림
        - 안잘리려면 `WITH TIES `절 사용

        ```sql
        SELECT TOP 5 WITH TIES 컬럼1,컬럼2 FROM 테이블
        ```

9. 특정 비율의 레코드 추출 TOP n Percent

    ```sql
    SELECT TOP 20 Percent * FROM 테이블
    ```

    > TOP(n)형태도 가능, n은 변수여도 사용 가능

    ```sql
    DECLARE @n INT=6;
    SELECT TOP(@n)  Percent WITH TIES *
     FROM 테이블 ORDER BY max_lvl DESC; -- with ties는 order문 없으면 사용불가
    ```

    

# 비교연산자

- 쿼리 수행시 >,<,=,>=,<= , **<>(같지않음-표준)**, **!=(같지않음-비문)** ,!>,!< 등 사용 가능

    ```sql
    SELECT * FROM 테이블 WHERE 컬럼 >= 20 -- 컬럼값이 20이상인 레코드만
    ```

- BETWEEN a AND b: 사이에 있는 값

    ```sql
    SELECT * FROM 테이블 WHERE 컬럼 BETWEEN 20 AND 40 ORDER BY 컬럼;
    ```

    - NOT BETWEEN도 가능하다
    - BETWEEN에 날짜도 사용 가능하다 BETWEEN '어제날짜' AND '내일날짜'

- 비교연산의 결과는 Boolean 데이터 형식이다
    - True, False, Unknown 세가지 값

```sql
DECLARE @p int; -- int 정수형 변수 선언
SET @p = 790; -- 변수에 값 할당
IF (@p <> 0) -- 조건문 @p가 0이 아닐때 True
	SELECT ID FROM Table1 WHERE ID = @p;
```



# 논리연산자

- AND, OR, NOT 등을 사용

    ```sql
    SELECT * FROM 테이블 WHERE 컬럼>10 and ID = 'x';
    ```

# 집계함수

- 통계 값. 각 레코드를 하나로 묶어서 하나의 단일 값 반환
- AVG(평균), COUNT(갯수), MAX(최댓값), MIN(최솟값), SUM(합계)

```sql
SELECT AVG(max_lvl) 평균 FROM jobs
```

```sql
SELECT COUNT(*) FROM title; -- 타이틀 테이블의 모든 레코드의 갯수
```

```sql
SELECT COUNT(price) FROM title; -- 타이틀 테이블에 price가 있는 레코드만 계산
```

> 뭔가 계산이 필요한 함수는 NULL이 있는 행은 제외한다.

#### NULL은 검색시 중요한 변수로 작용한다

- 예를들어 SUM 함수와 COUNT 함수를 같이 사용할 떄
    - 테이블의 책 값 평균
    - 테이블의 책 값이 있는 책들의 평균
        - 위 두 쿼리는 다른 결과가 나온다.
        - 2번쨰는 SELECT AVG(price) 또는 SELECT SUM(price) / COUNT(price)로 구할 수 있음
        - 1번쨰는 SELECT SUM(price) / COUNT(*)를 하면 가격이 없는 책들도 나눈다

#### NULL을 제외시키는 쿼리구성 하는 법

- ##### WHERE 조건절을 붙여서 NULL여부를 체크하면 된다

    ```sql
    SELECT COUNT(*) FROM 테이블 WHERE price IS NOT NULL;
    ```

#### <실습>

```sql
SELECT AVG(price) 평균 FROM titles; -- 14.7662

SELECT SUM(price) / COUNT(price) 평균2 FROM titles; -- 13.1255

SELECT SUM(price) / COUNT(*) 평균3 FROM titles; -- 14.7662

SELECT SUM(price) / COUNT(*) 평균4 FROM titles
	WHERE price IS NOT NULL; -- 14.7662
```





# GROUP BY, HAVING 절

### 집계함수만큼 자주 쓰이는 GROUP BY 절 (중요)

- 테이블에 어떤 컬럼이 카테고리(범주형)라면 범주별로 그룹을 만들 수 있다.
    - 그룹별 평균, 합계 등을 계산할 수 있다.
    - 즉 특정 컬럼을 기준으로 집계를 낸다는 뜻

- HAVING 절은 가진다, 뭔가 하고있는 것, 뭔가 조건을 더해주는 것

#### 기본형식

```sql
SELECT 그룹바이 절의 지정 컬럼, 집계할 값
FROM 테이블명
GROUP BY 그룹묶을 컬럼
```

##### <titles 테이블에서 type 별로 카테고리를 그룹지어서 평균을 출력하시오>

```sql
SELECT type, AVG(price) AS 평균
FROM dbo.titles
GROUP BY type;
```

결과 > ![image-20220309223304309](220309_TIL(SQLD).assets/image-20220309223304309.png) 

#### 정렬도 가능하다

- 단 평균으로 정렬시엔 주의해야 한다.

```sql
SELECT type, AVG(price) AS 평균
GROUP BY type
ORDER BY price DESC; -- 이렇게 쓰면 에러남 / 출력값이 평균이므로
ORDER BY 평균 DESC; -- 평균으로 정렬해야 된다
```

#### 그룹별로 카운트

```sql
SELECT type,COUNT(type) AS 개수, AVG(price) AS 평균
FROM titles
GROUP BY type
ORDER BY 개수 DESC;
```

###### 평균 책값 큰 TOP 3 출력

```sql
SELECT TOP 3 type, COUNT(type), AVG(price) FROM titles GROUP BY type
ORDER BY AVG(price) DESC;
```

###### 평균 책값 큰 TOP 3 정수형 (타입 변환은 convert(바꿀타입, 바꿀개체) 함수 사용)

```sql
SELECT TOP 3 type, COUNT(type), CONVERT(int,AVG(price))
FROM titles GROUP BY type ORDER BY AVG(price) DESC;
```

###### 평균 책값 큰 TOP 3 정수형 + 동점도 표시 

```sql
SELECT TOP 3 WITH TIES type, COUNT(type), CONVERT(int,AVG(price))
FROM titles GROUP BY type ORDER BY AVG(price) DESC; -- 이거 틀렸다
```

- 왜 틀렸나?
    - WITH TIES를 써도 동점인 레코드가 표시 안됨
    - ORDER BY를 실수형으로 정렬했기 때문에 동점이 아닌 것
    - ORDER BY CONVERT(int, AVG(price))로 하거나 아예 AS 평균해서 ORDER BY 평균

## HAVING

GROUP BY에서 조건을 달 떄는 HAVING 절을 사용

- GROUP BY로 가져온 결과에 원하는 값만 출력할 떄
- 해빙절은 그룹바이 절 뒤에 써야한다. (그룹바이 하고 나서 조건을 붙여야 되니까)
- 예) 카테고리 별 평균가격이 15달러 이상인 값만 출력

```sql
SELECT type,AVG(price) FROM titles
GROUP BY type
HAVING AVG(price) >= 15; -- WHERE 쓰면 오류남 / 그룹 값 계산이 안됨
```

- WHERE 절 > GROUP BY > HAVING > ORDER BY 순서를 꼭 지킬 것
- 전체에서 몇개 레코드 추출 > 타입별로 그룹 > 그룹 중에 일정 값 이상 > 정렬해서 출력



## GROUP BY ALL

- 조건에 맞는 값이 없는 경우도 있다
- 일반적으로 GROUP  BY는 NULL 값을 출력하지 않는다
- ALL 을 사용하면 NULL을 그대로 출력시켜준다 / 어떤 항목이 제외되었는지 나타낼 때 사용

```sql
SELECT type,AVG(price) FROM titles
WHERE price >= 21
1. GROUP BY type;
2. GROUP BY ALL type; -- 그룹값이 NULL인 카테고리도 출력
```

1.![image-20220309225126591](220309_TIL(SQLD).assets/image-20220309225126591.png) 2. ![image-20220309225139656](220309_TIL(SQLD).assets/image-20220309225139656.png) 



## GROUP BY ALL + NOT IN 사용

##### 예) 카테고리별 평균 책값을 출력, 단 mod_cook, trad_cook 카테고리는 제외하고

```sql
SELECT type, AVG(price) FROM titles
WHERE type NOT IN ('mod_cook','trad_cook')
GROUP BY type ORDER BY AVG(price) DESC;
```

- 위에서 GROUP BY ALL을 하면 모든 카테고리가 출력 된다
    - 단 WHERE로 제외된 카테고리는 평균을 안구하므로 NULL로 처리
    - ![image-20220309225711132](220309_TIL(SQLD).assets/image-20220309225711132.png) 



# 내일 할 게 많다... 분량이 너무 많음
