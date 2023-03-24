# 17강 - 삭제하기 - DELETE
DELETE FROM 테이블명 WHERE 조건식  
  
## 1. DELETE로 행 삭제하기
`DELETE FROM sample41;`으로 DELETE 명령을 실행하면 sample41테이블의 모든 데이터가 삭제됩니다.  
WHERE 구를 생략할 경우에는 모든 행을 대상으로 동작하기 때문입니다.  
삭제는 행 단위로 수행됩니다. SELECT 명령과 같이 열을 지정할 수는 없습니다.  
  
## 2. DELETE 명령 구
`DELETE FROM sample41 WHERE no = 1 OR no = 2;`  
ORDER BY 구는 사용할 수 없습니다. 어떤 행부터 삭제할 것인지는 중요하지 않으며 의미가 없기 때문입니다.  
* MySQL에서는 DELETE 명령에서 ORDER BY 구와 LIMIT 구를 사용ㅎ 삭제할 행을 지정할 수 있습니다.  
