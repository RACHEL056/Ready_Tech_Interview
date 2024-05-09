## SQL 개요

Structured Query Language

비절차적 언어로 자신이 원하는 것(what)을 명시하며 처리하는 방법(how)는 명시할 수 없음

자연어에 가까운 구문을 사용해 질의 표현 가능

두가지 인터페이스 → 1. 대화식 SQL(interactive SQL) 2. 내포된 SQL(embedded SQL)

### 구성요소

- 데이터 정의어
- 데이터 조작어
- 데이터 제어어

### 데이터 정의어 - CREATE, ALTER, DROP

- 스키마의 생성과 제거
    
    ```sql
    //Kim이 권한을 가진 스키마 생성
    CREATE SCHEMA MY_DB AUTHORIZATION KIM
    
    //삭제 하지 마라
    DROP SCHEMA MY_DB RESTRICT;
    //무조건 다 삭제, 연쇄적으로 삭제됨
    DROP SCHEMA MY_DB CASCADE;
    ```
    
- 릴레이션 정의 - 데이터 타입, 외래키, 기본키 등등
- 릴레이션 제거
    
    ```sql
    DROP TABLE DEPARTMENT;
    ```
    
- ALTER TABLE
    
    ```sql
    ALTER TABLE EMPLOYEE ADD PHONE CHAR(13);
    ```
    
- 인덱스 생성
    
    ```sql
    CREATE INDEX EMPDNO_INX ON EMPLOYEE(DNO)
    ```
    
- 도메인 생성
    
    ```sql
    CREATE DOMAIN DEPTNAME CHAR(10) DEFAULT '개발';
    ```
    
- 제약조건 - NOT NULL, UNIQUE, DEFAULT, CHECK(데이터 타입에 제약조건)…
    
    ```sql
    CREATE TABLE EMPLOYEE
        (EMPNO INTEGER NOT NULL,
        EMPNAME CHAR(10) UNIQUE,
        TITLE CHAR(10) DEFAULT '사원',
        MANAGER INTEGER,
        SALARY INTEGER CHECK (SALARY < 600000),
        DNO INTEGER CHECK (DNO IN (1,2,3,4)) DEFAULT 1,
        PRIMARY KEY(EMPNO),
        FOREIGN KEY(MANAGER) REFERENCES EMPLOYEE(EMPNO),
        FOREIGN KEY(DNO) REFERENCES DEPARTMENT(DEPTNO)
                        ON DELETE SET DEFAULT ON UPDATE CASCADE); //Delete시 디폴트 값으로 변경하고 update 시 모든 연결된 릴레이션 값 변경
        
    ```
    
    - 참조 무결성 제약조건 - NO ACTION, CASCADE, SET NULL, SET DEFUALT

### SELECT 문 (SELECT 애트리뷰트(들) FROM 릴레이션(들))

SELECT ~ FROM ~ WHERE ~ GROUP BY ~ HAVING ~ ORDER BY ~

- 별칭(alias)
    
    ```sql
    FROM EMPLOYEE AS E, DEPARTMENT AS D
    ```
    
- 와일드 카드 “*” → 모든 어트리뷰트 원할 시 SELECT *
- 문자열 비교
    
    ```sql
    //exact match가 아닌 substring match
    WHERE EMPNAME LIKE '이%';
    ```
    
- 연산자 우선순위: 비교 연산자 > NOT > AND > OR
- 범위 조건
    
    ```sql
    WHERE SALARY BETWEEN 3000 AND 45000;
    
    =
    
    WHERE SALARY >= 3000 AND SALARY <= 45000;
    ```
    
- SELECT 절에서 산술 연산자(+, -, *, / ) 사용 가능
    
    ```sql
    SELECT EMPNAME, SALARY, SALARY * 1.1 AS NEWSALARY
    ```
    
- 널 값 검색 가능,  다만 COUNT(*)시 NULL 값은 무시된다
    
    ```sql
    //X
    NULL > 300
    NULL = 300
    NULL <>300
    NULL = NULL
    NULL <> NULL
    
    //O
    WHERE DNO IS NULL;
    ```
    
- ORDER BY로 Sorting 가능 오름차순 Default, 내림차순도 가능(DESC)
- 집단 함수 - AVG, MAX …
- 집합 연산 - 두 릴레이션이 input
    - UNION(합집합), EXCEPT(차집합), INTERSECT(교집합), UNION ALL(중복 허용 합집합), EXCEPT ALL(중복 허용 차집합), INTERSECT ALL
- 조인
    - 두 개 이상의 릴레이션으로 연관된 튜플들을 결합
    - 조인 조건이 없을 경우 카티션 곱 생성
    - 자체 조인(self joint) - 한 릴레이션에 속하는 튜플을 동일한 릴레이션에 속하는 튜플과 조인
        
        ```sql
        SELECT E.EMPNAME, M.EMPNAME
        FROM EMPLOYEE E, EMPLOYEE M //이때 한가지 릴레이션에 다른 이름을 줌
        WHERE E.MANAGER = M.EMPNO;
        ```
        
- 중첩 질의(부질의 subquery) - WHERE 절에 다시 SELECT … FROM … WHERE 형태로 포함됨
    - 하나의 스칼라 값만 나오는 질의
    - 절차적 성격 → IN, ANY, ALL
    - 여러 애트리뷰트들로 이루어진 릴레이션 반환 경우
        
        → EXISTS(튜플이 하나라도 있는 것)
        
    - 상관 중첩 질의(correlated nested query) - 안쪽 subquery가 바깥쪽 query와 상관 관계
    
    ** nested loop 일 경우 튜플의 수가 적은 릴레이션 기준으로 카티션 곱이 진행되야 성능이 좋다
