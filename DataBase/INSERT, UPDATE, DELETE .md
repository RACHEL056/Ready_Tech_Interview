### INSERT, DELETE, UPDATE

- INSERT - 기존의 릴레이션에 튜플 삽입
    
    ```sql
    INSERT INTO DEPARTMENT
    VALUES (5, '연구', ''); //이때 필수 attribute들만 가지고 삽입 가능
    ```
    
    - 릴레이션에 한 번에 여러 개의 튜플들 삽입
        
        ```sql
        INSERT
        INTO 릴레이션(attribute1, ...., attribute n)
        SELECT ... FROM ... WHERE ...;
        ```
        
- DELETE - 한 릴레이션에서 한 개 이상 튜플 삭제 (DELETE FROM 릴레이션 WHERE 조건;)
- UPDATE - 한 릴레이션에 들어 있는 튜플들의 애트리뷰트 값 수정
    
    (UPDATE 릴레이션 SET 애트리뷰트 = 값 or 식 WHERE 조건;)
