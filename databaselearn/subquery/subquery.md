# 서브쿼리
쿼리를 작성할 때 서브쿼리를 작성하면 **단위 처리별로 쿼리를 독립적으로** 작성할 수 있다.  
조인처럼 여러 테이블을 섞어 두는 형태가 아니어서 쿼리의 가독성도 높아지며, 복잡한 쿼리도 손쉽게 작성할 수 있다.  
  
서브쿼리는 쿼리의 여러 위치에서 사용될 수 있는데, 대표적으로 SELECT 절과 FROM절, WHERE절에 사용될 수 있다.  
하지만 사용하는 위치에 따라 쿼리의 성능 영향도와 MySQL 서버의 최적화 방법은 완전히 달라진다.  
  
## SELECT 절에 사용된 서브쿼리
SELECT 절에 사용된 서브쿼리는 내부적으로 임시 테이블을 만들거나 쿼리를 비효율적으로 실행하게 만들지는 않기 때문에 서브쿼리가 적절히 인덱스를 사용할 수 있다면 크게 주의할 사항은 없다.  
  
일반적으로 SELECT 절에 서브쿼리를 사용하면 **그 서브쿼리는 항상 칼럼과 레코드가 하나인 결과를 반환해야 한다.(스칼라 서브쿼리)**  
그 값이 NULL 이든 아니든 관계없이 레코드가 1건이 존재해야 한다는 것인데, MySQL에서는 이 체크 조건이 조금은 느슨하다.  

```SQL
SELECT emp_no, (SELECT dept_name FROM departments WHERE dept_name = 'Sales1')
FROM dept_emp LIMIT 10;

SELECT emp_no, (SELECT dept_name FROM departments)
FROM dept_emp LIMIT 10;
-- [21000][1242] Subquery returns more than 1 row

SELECT emp_no, (SELECT dept_no, dept_name FROM  departments WHERE dept_name = 'Sales1')
FROM dept_emp LIMIT 10;
-- [21000][1241] Operand should contain 1 column(s)
```
- 첫 번째 서브쿼리에서 사용된 서브쿼리는 항상 결과가 0건이다. 하지만 첫 번째 쿼리는 에러를 발생시키지 않고, 서브쿼리의 결과는 NULL로 채워져서 반환된다.  
- 두 번째 쿼리에서 서브쿼리가 2건 이상의 레코드를 반환하는 경우에는 에러가 나면서 쿼리가 종료된다.  
- 세 번째 쿼리와 같이 SELECT 절에 사용된 서브쿼리가 2개 이상의 칼럼을 가져오려고 할 때도 에러가 발생한다.  
  
즉, SELECT 절의 서브쿼리에는 로우 서브쿼리를 사용할 수 없고, 오로지 스칼라 서브쿼리만 사용할 수 있다.  
  

