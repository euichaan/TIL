# 22강 - 그룹화 - GROUP BY
그룹화를 통해 집계함수의 활용범위를 넓힐 수 있습니다.  
`SELECT * FROM 테이블명 GROUP BY 열1, 열2, ...`  
GROUP BY 구를 사용해 집계함수로 넘겨줄 집합을 그룹으로 나누는 방법을 설명하겠습니다.  
  
## 1. GROUP BY로 그룹화
그룹으로 나눌 때에는 GROUP BY 구를 사용합니다. 이때 GROUP BY 구에는 그룹화할 열을 지정합니다.  
`SELECT name FROM sample51 GROUP BY name;`  
GROUP BY 구에 열을 지정하여 그룹화하면 지정된 열의 값이 같은 행이 하나의 그룹으로 묶입니다.  
  
GROUP BY 구를 지정하는 경우에는 집계함수와 함께 사용하지 않으면 별 의미가 없습니다.  
GROUP BY 구로 그룹화된 각각의 그룹이 하나의 집합으로서 집계함수의 인수로 넘겨지기 때문입니다.  
`SELECT name, count(name), SUM(quantity) FROM sample51 GROUP BY name;`  
  
## 2. HAVING 구로 조건 지정
집계함수는 WHERE 구의 조건식에서는 사용할 수 없습니다.  
`SELECT name, COUNT(name) FROM sample51 WHERE COUNT(name)=1 GROUP BY name; // 에러`    
name열을 그룹화하여 행 개수가 하나만 존재하는 그룹을 검색하고 싶었지만 에러가 발생하여 실행할 수 없습니다.  
-> WHERE 구로 행을 검색하는 처리가 GROUP BY로 그룹화하는 처리보다 순서상 앞서기 때문입니다.  
그룹화가 필요한 집계함수는 WHERE 구에서 사용할 수 없습니다.  
  
HAVING 구를 사용하면 집계함수를 사용해서 조건식을 지정할 수 있습니다.  
HAVING 구는 GROUP BY 구의 뒤에 기술하며 WHERE 구와 동일하게 조건식을 지정할 수 있습니다.  
조건식에는 그룹별로 집계된 열의 값이나 집계함수의 계산결과가 전달된다고 생각하면 이해하기 쉽습니다.  
이때 조건식이 참인 그룹값만 클라이언트에게 전달됩니다.  
`SELECT name, COUNT(name) FROM sample51 GROUP BY name HAVING COUNT(name)=1;`  
  
집계함수를 사용할 경우 HAVING 구로 검색조건을 지정한다!  
  
내부 처리 순서(WGHSO) : WHERE 구 -> GROUP BY 구 -> HAVING 구 -> SELECT 구 -> ORDER BY 구  
  
그룹화보다도 나중에 처리되는 ORDER BY 구에서는 문제없이 집계함수를 사용할 수 있습니다.  
즉, ORDER BY COUNT(name)과 같이 지정할 수 있습니다.  
  
## 3. 복수열의 그룹화
GROUP BY를 사용할 때 주의할 점이 하나 더 있습니다.  
GROUP BY에 지정한 열 이외의 열은 집계함수를 사용하지 않은 채 SELECT 구에 기술해서는 안 됩니다.  
`SELECT no,name,quantity FROM sample51 GROUP BY name; //에러`    
name 열을 그룹화했으므로 SELECT 구에 name을 지정하는 것은 문제없지만, no열이나 quantity열을 SELECT 구에 지정하면 데이터베이스  
제품에 따라 에러가 발생합니다.  
  
GROUP BY로 그룹화하면 클라이언트로 반환되는 결과는 그룹당 하나의 행이 됩니다.  
하지만 name 열 값인 A인 그룹의 quantity 열 값은 1과 2로 두 개입니다. 이때 그룹마다 하나의 값만을 반환해야 하므로 어느 것을 반환할지 몰라  
에러가 발생합니다. 이때 집계함수를 사용하면 집합은 하나의 값으로 계산되므로, 그룹마다 하나의 행을 출력할 수 있습니다.  
`SELECT MIN(no), name, SUM(quantity) FROM sample51 GROUP BY name;`  
  
GROUP BY에서 지정한 열 이외의 열은 집계함수를 사용하지 않은 채 SELECT 구에 지정할 수 없다!  
  
만약 no와 quantity로 그룹화한다면 GROUP BY no, quantity로 지정합니다.  
이처럼 GROUP BY에서 지정한 열이라면 SELECT 구에 그대로 지정해도 됩니다.  
`SELECT no,quantity FROM sample51 GROUP BY no,quantity;`  
  
## 4. 결괏값 정렬
GROUP BY로 그룹화해도 실행결과 순서를 정렬할 수는 없습니다. GROUP BY 지정을 해도 정렬되지는 않는다!  
ORDER BY 구를 사용해 정렬할 수 있습니다.  
`SELECT name, COUNT(name), SUM(quantity) FROM sample51 GROUP BY name ORDER BY SUM(quantity) DESC;`  
