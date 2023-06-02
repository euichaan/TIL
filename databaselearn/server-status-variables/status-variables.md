# MySQL 서버 상태 변수
상태 값 수집은 다음과 같이 실행 할 수 있다.  
```SQL
FLUSH STATUS;

-- // 각 쿼리 실행

SHOW STATUS LIKE 'Handler_%';
```
`FLUSH STATUS`: status indicators(상태 표시기)를 플러시합니다. 이 작업은 현재 스레드의 세션 상태 변수 값을 전역 값에 추가하고 세션 값을 0으로 재설정합니다. 일부 전역 변수도 0으로 재설정될 수 있습니다. 또한 키 캐시의 카운터를 0으로 재설정하고 Max_used_connections를 현재 open connection의 수로 설정합니다.  
`SHOW STATUS`: 서버 상태 정보를 제공합니다. 이 statement에서는 권한이 필요하지 않습니다. 서버에 연결할 수 있는 기능만 있으면 됩니다.  
  
[MySQL Server Status Variables](https://dev.mysql.com/doc/refman/8.0/en/server-status-variables.html)  
  
주요한 상태 변수는 다음과 같다.  
- Handler_read_key  
키를 기반으로 행을 읽기 위한 요청 수입니다. 이 값이 높으면 테이블이 쿼리에 대해 적절하게 인덱싱되었음을 나타내는 좋은 지표입니다.  
    
- Handler_read_next  
키 순서에서 다음 행을 읽기 위한 요청 수입니다. 범위 제약이 있는 인덱스 열을 쿼리하거나 인덱스 스캔을 수행하는 경우 이 값이 증가합니다.  
     
- Handler_write  
테이블에 행을 삽입하기 위한 요청 수  
