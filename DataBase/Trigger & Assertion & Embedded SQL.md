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
