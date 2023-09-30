# INSERT만 실행되는 로그 테이블의 구조를 변경하기
다음과 같은 구조의 INSERT만 실행되는 로그 테이블을 가정해보자.
```SQL
CREATE TABLE access_log (
    id BIGINT NOT NULL AUTO_INCREMENT,
    client_id INT UNSIGNED,
    access_dttm TIMESTAMP,
    ...
    PRIMARY KEY(id)
);
```
그런데 어느 날 이 테이블의 구조를 변경해야 할 요건이 발생했다.  
물론 MySQL 서버의 Online DDL을 이용해서 변경할 수도 있지만 시간이 너무 오래 걸리는 경우라면 언두 로그의 증가와 Online DDL이 실행되는 동안 누적된 Online DDL 버퍼의 크기 등 고민해야 할 문제가 많다.  
  
더 큰 문제는 MySQL 서버의 DDL은 단일 스레드로 작동하기 때문에 상당히 많은 시간이 소모될 것이라는 점이다.  
이때는 새로운 구조의 테이블을 생성하고 먼저 최근(1시간 직전 또는 하루 전)의 데이터까지는 프라이머리 키인 id 값을 범위별로 나눠서 여러 개의 스레드로 빠르게 복사한다.  
  
```SQL
CREATE TABLE access_log_new (
    id BIGINT NOT NULL AUTO_INCREMENT,
    client_id INT UNSIGNED,
    access_dttm TIMESTAMP,
    ...
    PRIMARY KEY(id)
) KEY_BLOCK_SIZE=4;

-- // 4개의 스레드를 이용해 id 범위별로 레코드를 신규 테이블로 복사
INSERT INTO access_log_new SELECT * FROM access_log WHERE id>=0 AND id<10000;
INSERT INTO access_log_new SELECT * FROM access_log WHERE id>=10000 AND id<20000;
INSERT INTO access_log_new SELECT * FROM access_log WHERE id>=20000 AND id<30000;
INSERT INTO access_log_new SELECT * FROM access_log WHERE id>=30000 AND id<40000;
```
그리고 나머지 데이터는 다음과 같이 트랜잭션과 테이블 잠금, RENAME TABLE 명령으로 응용 프로그램의 중단 없이 실행할 수 있다. 이때 "남은 데이터를 복사"하는 시간 동안은 테이블의 잠금으로 인해 INSERT를 할 수 없게 된다. 그래서 가능하면 미리 아주 최근 데이터까지 복사해 둬야 잠금 시간을 최소화해서 서비스에 미치는 영향을 줄일 수 있다.  
  
```SQL
-- // 트랜잭션을 autocommit으로 실행(BEGIN이나 START TRANSACTION으로 실행하면 안 됨)
SET autocommit=0;

-- // 작업 대상 테이블 2개에 대해 테이블 쓰기 락을 획득
LOCK TABLES access_log WRITE, access_log_new WRITE;

-- // 남은 데이터를 복사
SELECT MAX(id) as @MAX_ID FROM access_log_new;
INSERT INTO access_log_new SELECT * FROM access_log WHERE pk>@MAX_ID;
COMMIT;

-- // 새로운 테이블로 데이터 복사가 완료되면 RENAME 명령으로 새로운 테이블을 서비스로 투입
RENAME TABLE access_log TO access_log_old, access_log_new TO access_log;

-- // 불필요한 테이블 삭제
DROP TABLE access_log_lod;
```
