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
스칼라 서브쿼리는 레코드와 칼럼이 각각 하나인 결과를 만들어내는 서브쿼리이며, 로우(레코드) 서브쿼리는 스칼라 서브쿼리보다 레코드 건수가 많거나 칼럼 수가 많은 결과를 만들어 내는 서브쿼리이다.  
  
가끔 조인으로 처리해도 되는 쿼리를 SELECT 절의 서브쿼리를 사용해서 작성할 때도 있다.  
하지만 서브쿼리로 실행될 때보다 조인으로 처리할 때가 조금 더 빠르기 때문에 가능하다면 조인으로 쿼리를 작성하는 것이 좋다.  
  
```SQL
SELECT
    COUNT(CONCAT(e1.first_name,
                 (SELECT e2.first_name FROM employees e2 WHERE e2.emp_no = e1.emp_no))
        ) FROM employees e1;

SELECT COUNT(CONCAT(e1.first_name, e2.first_name))
FROM employees e1, employees e2
WHERE e1.emp_no=e2.emp_no;
```
위의 두 예제 쿼리 모두 employees 테이블을 두 번씩 프라이머리 키를 이용해 참조하는 쿼리다. 물론 위 emp_no는 프라이머리 키라서 조인이나 서브쿼리 중 어떤 방식을 사용해도 같은 결과를 가져온다.  
- 서브쿼리: 평균 0.78초  
- 조인: 0.65초  
  
처리해야 하는 레코드 건수가 많아지면 많아질수록 성능 차이가 커질수도 있으므로 가능하면 조인으로 쿼리를 작성하는 방법을 권장한다.  
  
그리고 가끔 다음 예제와 같이 SELECT절에 서브쿼리가 사용되는 경우에 동일한 서브쿼리가 여러 번 사용되기도 한다.  
```SQL
SELECT e.emp_no, e.first_name,
       (SELECT s.salary FROM salaries s
        WHERE s.emp_no = e.emp_no
        ORDER BY s.from_date DESC LIMIT 1) AS salary,
       (SELECT s.from_date FROM salaries s
        WHERE s.emp_no = e.emp_no
        ORDER BY s.from_date DESC LIMIT 1) AS salary_from_date,
       (SELECT s.to_date FROM salaries s
        WHERE s.emp_no = e.emp_no
        ORDER BY s.from_date DESC LIMIT 1) AS salary_to_date
    FROM employees e
    WHERE e.emp_no=499999;
```
이 쿼리의 경우, LIMIT 1 조건 때문에 salaries 테이블을 조인으로 사용할 수가 없었다.  
하지만 MySQL 8.0 버전부터 도입된 **래터럴 조인**을 이용하면 이렇게 동일한 레코드의 각 칼럼을 가져오기 위해서 서브쿼리를 3번씩이나 남용하지 않아도 된다.  
```SQL
SELECT e.emp_no, e.first_name,
       s2.salary, s2.from_date, s2.to_date
       FROM employees e 
        INNER JOIN LATERAL ( 
            SELECT * FROM salaries s 
            WHERE s.emp_no = e.emp_no
            ORDER BY s.from_date DESC
           LIMIT 1) s2 ON s2.emp_no=e.emp_no
        WHERE e.emp_no=499999;
```
3번의 서브쿼리를 하나의 래터럴 조인으로 변경했기 때문에 이제 salaries 테이블을 한 번만 읽어서 쿼리를 처리할 수 있다.  
  
래터럴 조인에는 한 가지 문제점이 있다. Handler_read_next 값이 증가한다는 점이다.  
Handler_read_next는 인덱스를 사용하지 않고 순차적으로 데이터를 읽은 횟수를 나타낸다.  
  
래터럴 조인을 사용한 쿼리의 실행 계획을 살펴보면, "Using filesort"가 표시된 것을 확인할 수 있다.  
인덱스를 이용해 충분히 정렬된 결과를 가져올 수 있음에도 불구하고 정렬을 실행했으며, Handler_read_next 값이 증가했다.  
이는 MySQL 8.0 버전의 버그로 식별됐는데, 아직 이 버그는 해결되지 않은 상태다.  
  
## FROM 절에 사용된 서브쿼리
이전 버전의 MySQL에서는 FROM 절에 서브쿼리가 사용되면 항상 서브쿼리의 결과를 임시 테이블로 저장하고 필요할 때 다시 임시 테이블을 읽는 방식으로 처리했다.  
그래서 가능하면 FROM 절의 서브 외부 쿼리로 병합하는 형태로 쿼리 튜닝을 했다.  
  
하지만 MySQL 5.7 버전부터는 옵티마이저가 FROM 절의 서브쿼리를 외부 쿼리로 병합하는 최적화를 수행하도록 개선됐다.  
  
EXPLAIN 명령을 실행하고 SHOW WARNINGS 명령을 실행하면 MySQL 서버가 서브쿼리를 병합해서 재작성한 쿼리의 내용을 확인할 수 있다.  
  
- EXPLAIN: 쿼리의 실행 계획을 분석하여 옵티마이저가 어떻게 쿼리를 처리할 것인지에 대한 정보를 제공하는 명령  
- SHOW WARNINGS: 직전에 실행된 쿼리에서 발생한 경고를 보여주는 명령  
```SQL
EXPLAIN SELECT * FROM (SELECT * FROM employees) y;
```
서브쿼리의 외부 쿼리 병합은 꼭 FROM 절의 서브쿼리에 대해서만 적용되는 최적화는 아니다. FROM 절에 사용된 뷰(View)의 경우에도 MySQL 옵티마이저는 MySQL 옵티마이저는 뷰 쿼리와 외부 쿼리를 병합해서 최적화된 실행 계획을 사용한다.  
  
FROM 절의 모든 서브쿼리를 외부 쿼리로 병합할 수 있는 것은 아니다.  
다음과 같은 기능이 서브쿼리에 사용되면 FROM 절의 서브쿼리는 외부 쿼리로 병합되지 못한다.  
- 집합 함수 사용(SUM(), MIN(), MAX(), COUNT() 등)  
- DISTINCT  
- GROUP BY 또는 HAVING  
- LIMIT  
- UNION(UNION DISTINCT) 또는 UNION ALL  
- SELECT 절에 서브쿼리가 사용된 경우  
- 사용자 변수 사용(사용자 변수에 값이 할당되는 경우)  
  
외부 쿼리와 병합되는 FROM 절의 서브쿼리가 ORDER BY 절을 가진 경우에는 외부 쿼리가 GROUP BY나 DISTINCT 같은 기능을 사용하지 않는다면 서브쿼리의 정렬 조건을 외부 쿼리로 같이 병합한다.  
외부 쿼리에서 GROUP BY나 DISTINCT와 같은 기능이 사용되고 있다면, 서브쿼리의 정렬 작업은 무의미하기 때문에 서브쿼리의 ORDER BY 절은 무시된다.  
  
MySQL 서버에서 FROM 절의 서브쿼리를 외부 쿼리로 병합하는 최적화는 optimizer_switch 시스템 변수로 제어할 수 있다.  
## WHERE 절에 사용된 서브쿼리
WHERE 절의 서브쿼리는 SELECT 절이나 FROM 절보다는 다양한 형태로 사용될 수 있는데, 크게 다음 3가지로 구분해서 살펴보겠다.  
이는 MySQL 옵티마이저가 최적화하는 형태를 기준으로 구분해본 것이다.  
- 동등 또는 크다 작다 비교 = subquery  
- IN 비교 = IN subquery  
- NOT IN 비교 = NOT IN subquery  
  
### 동등 또는 크다 작다 비교
MySQL 5.5 이전 버전까지는 서브쿼리 외부의 조건으로 쿼리를 실행하고, 최종적으로 서브쿼리를 체크 조건으로 사용했다.  
하지만 이러한 처리 방식의 경우 풀 테이블 스캔이 필요한 경우가 많아서 성능 저하가 심각했다.  
다음 예제 쿼리를 한번 살펴보자.  
```SQL
SELECT * FROM dept_emp de
    WHERE de.emp_no = (SELECT e.emp_no
                       FROM employees e
                       WHERE e.first_name='Georgi' AND e.last_name='Facello' LIMIT 1)
```
MySQL5.5 이전 버전까지는 위 쿼리의 경우 dept_emp 테이블을 풀 스캔하면서 서브쿼리의 조건에 일치하는지 여부를 체크했다. 하지만 이는 많은 사용자가 기대하는 실행 순서는 아니었을 것이다.  
  
MySQL 5.5 버전부터는 이 쿼리의 실행 계획은 그 이전 버전과는 정반대로 실행되도록 개선됐다.  
**서브쿼리를 먼저 실행한 후 상수로 변환한다.** 그리고 상숫값으로 서브쿼리를 대체해서 나머지 쿼리 부분을 처리한다.  
그래서 위 쿼리의 경우 다음과 같은 실행 계획을 사용한다.    
EXPLAIN 키워드를 붙여서 확인할 수 있다.  
```
| id | select_type | table | partitions | type | possible_keys     | key               | key_len | ref   | rows | filtered | Extra       |
+----+-------------+-------+------------+------+-------------------+-------------------+---------+-------+------+----------+-------------+
|  1 | PRIMARY     | de    | NULL       | ref  | ix_empno_fromdate | ix_empno_fromdate | 4       | const |    1 |   100.00 | Using where |
|  2 | SUBQUERY    | e     | NULL       | ref  | ix_firstname      | ix_firstname      | 58      | const |  253 |    10.00 | Using where
```
참고로, ref는 조인을 할 때 Primary Key 혹은 Unique Key가 아닌 Key로 매칭하는 경우를 말한다.  
ALL은 테이블을 처음부터 끝까지 검색하는 경우로, 풀 테이블 스캔이라고 한다.  
  
위의 실행 계획에 의하면 dept_name 테이블을 풀 스캔하지 않고 (emp_no, from_date) 조합의 인덱스를 사용했다는 것을 알 수 있다.  
이를 위해서는 서브쿼리 부분이 먼저 처리됐어야 한다는 것도 예측할 수 있다.  
  
조금 더 명확히 순서를 확인해보기 위해서는 EXPLAIN ANALYZE 명령어을 추가해서 쿼리의 실행 계획을 확인해보면 된다.  
```
-> Filter: (de.emp_no = (select #2))  (cost=1.10 rows=1) (actual time=0.016..0.018 rows=1 loops=1)
    -> Index lookup on de using ix_empno_fromdate (emp_no=(select #2))  (cost=1.10 rows=1) (actual time=0.016..0.017 rows=1 loops=1)
    -> Select #2 (subquery in condition; run only once)
        -> Limit: 1 row(s)  (cost=65.78 rows=1) (actual time=0.271..0.271 rows=1 loops=1)
            -> Filter: (e.last_name = 'Facello')  (cost=65.78 rows=25) (actual time=0.270..0.270 rows=1 loops=1)
                -> Index lookup on e using ix_firstname (first_name='Georgi')  (cost=65.78 rows=253) (actual time=0.269..0.269 rows=1 loops=1)
```
제일 하단의 제일 안쪽 "Index  lookup on e using ix_firstname"라인을 확인할 수 있다. 즉, ix_firstname 인덱스로 서브쿼리를 처리한 후, 그 결과를 이용해 dept_emp 테이블의 ix_empno_fromdate 인덱스를 검색해 쿼리가 완료된다는 것을 의미한다.  
  
동등 비교 대신 크다 또는 작다 비교가 사용돼도 동일한 실행 계획을 사용한다.  
  
다음과 같이 단일 값 비교가 아닌 튜플 비교 방식이 사용되면 서브쿼리가 먼저 처리되어 상수화되긴 하지만 외부 쿼리는 인덱스를 사용하지 못하고 풀 테이블 스캔을 실행하는 것을 확인할 수 있다. MySQL 8.0 버전이라고 하더라도 아직 **튜플 형태의 비교는 주의해서 사용해야 한다.**   
  
```SQL
EXPLAIN
SELECT *
FROM dept_emp de WHERE (emp_no, from_date) = (
        SELECT emp_no, from_date
        FROM salaries
        WHERE emp_no = 100001 LIMIT 1);
```
  
### IN 비교 (IN subquery)
실제 조인은 아니지만 다음 예제와 같이 테이블의 레코드가 다른 테이블의 레코드를 이용한 표현식(또는 칼럼 그 자체)과 일치하는지를 체크하는 형태를 세미 조인(Semi-Join)이라고 한다. 즉 WHERE 절에 사용된 IN (subquery) 형태의 조건을 조인의 한 방식인 세미 조인이라고 보는 것이다.  
```SQL
SELECT *
FROM employees e
WHERE e.emp_no IN
    (SELECT de.emp_no FROM dept_emp de WHERE de.from_date='1995-01-01');
```
MySQL5.5 버전까지는 세미 조인의 최적화가 매우 부족해서 대부분 풀 테이블 스캔을 했다.  
하지만 MySQL 5.6 버전부터 8.0버전까지 세미 조인의 최적화가 많이 개선되면서 이제 더 이상은 IN (subquery) 형태를 2개의 쿼리로 쪼개서 실행하거나 다른 우회 방법을 찾을 필요가 없어졌다.  
  
### NOT IN 비교 (NOT IN subquery)
IN (subquery) 와 비슷한 형태지만 이 경우를 안티 세미 조인(Anti Semi-Join)이라고 명명한다. 일반적인 RDBMS에서 Not-Equal 비교(<>)는 인덱스를 제대로 활용할 수 없듯이 안티 세미 조인 또한 최적화할 수 있는 방법이 많지 않다.  
MySQL 옵티마이저는 안티 세미 조인 쿼리가 사용되면 다음 두 가지 방법으로 최적화를 수행한다.  
- NOT EXISTS  
- 구체화(Materialization)  
  
두 가지 최적화 모두 성능 향상에 도움이 되지 않는 방법이므로 쿼리가 최대한 다른 조건을 활용해서 데이터 검색 범위를 좁힐 수 있게 하는 것이 좋다. WHERE 절에 단독으로 안티 세미 조인 조건만 있다면 **풀 테이블 스캔을 피할 수 없으니 주의하자.**  
  