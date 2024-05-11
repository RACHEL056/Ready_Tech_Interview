### Trigger & assertion 트리거와 주장

Trigger - 명시된 이벤트가 발생할 때마다 DBMS가 자동적으로 수행하는 사용자 정의문(프로시저)

이벤트 - 조건 - 동작(ECA) 규칙

```sql
//SQL3
CREATE TRIGGER <트리거 이름>
AFTER <트리거를 유발하는 이벤트 연결 리스트> on <릴레이션> 
[WHEN <조건>]
BEGIN <SQL문(들)> END

```

- 연쇄적으로 활성화 되는 트리거 가능

Assertion - 제약조건을 위반하는 연산이 수행되지 않도록 함

트리거보다 더 강력한, 일반적인 무결성 제약조건

```sql
CREATE ASSERTION 이름
CHECK 조건 ;
```

### 내포된 SQL

SQL → 호스트 언어의 완전한 표현력을 갖고 있지 않기 때문에 모든 질의 SQL로 표현할 수 없음

따라서 C, C++ 등의 언어로 작성하는 프로글매에 SQL 삽입 ⇒ 호스트 언어에 포함되는 SQL = 내포된 SQL

- C 언어에 SQL내포 = ESQL/C
- 커서(cursor) - 한번에 한 튜플씩 가져오는 수단
    - DECLARE을 통해 커서를 정의 후 OPEN으로 질의 수행, FETCH로 다음 튜플로 이동하고 CLOSE로 닫게 된다.
- DBMS와 프로그래밍 언어 사이에 소통하는 공간 필요: SQLCA(SQL Communications Area)
    - SQLCA 데이터 구조 중 가장 널리 사용되는 필드 → SQLCODE 변수
        
        이때 SQLCODE 값이 0 이면 마지막에 내포된 SQL 문이 성공적으로 끝났음을 의미
        
        ```sql
        EXEC SQL DECLARE c1 CURSOR FOR
            SELECT empno, enpname, title, manager, salary, dno
            FROM employee;
            EXEC SQL OPEN c1;
            while (SQLCODE == 0)
            {
            EXEC SQL
                FETCH c1 INTO :eno, :name, :title, :manager, :salary, :dno;
                if (SQLCODE == 0)
                    printf("SUCCESSFUL");
        }
        EXEC SQL CLOSE c1;
        
        //따라서 데이터 성공적으로 가져올 수 있으면 while 문을 돌고 이후 가져오지 못할 경우 SQLCODE
        //값이 1이 되므로 반복문을 빠져나오게 된다.
        ```
