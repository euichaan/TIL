# 15강 - CASE 문으로 데이터 변환하기
CASE 문을 이용해 데이터를 변환할 수 있습니다.  
  
```SQL
CASE WHEN 조건식1 THEN 식1
    WHEN 조건식2 THEN 식2
    ELSE 식3
END
```
1 -> 남자  
2 -> 여자  
```SQL
CASE
  WHEN 1 THEN '남자'
  WHEN 2 THEN '여자'
END
```
RDBMS에 준비된 함수를 이용해 데이터를 특정 형태로 변환하는 경우도 있지만, 임의의 조건에 따라 `독자적으로 변환 처리를 지정해` 데이터를 변환하고  
싶은 경우도 있을 겁니다. 이때 CASE 문을 이용할 수 있습니다.  
  
## 1. CASE문
예를 들면 NULL 값을 0으로 간주하여 계산하고 싶은 경우.  
간단한 처리의 경우에는 사용자 정의 함수를 작성하지 않고도 CASE문으로 처리할 수 있습니다.  
  
```SQL
CASE WHEN 조건식1 THEN 식1
    WHEN 조건식2 THEN 식2
    ELSE 식3
END
```
먼저 WHEN절에는 참과 거짓을 반환하는 조건식을 기술합니다. 해당 조건을 만족하여 참이 되는 경우는 THEN 절에 기술한 식이 처리됩니다.  
이때 WHEN과 THEN을 한데 조합해 지정할 수 있습니다. WHEN 절의 조건식을 차례로 평가해 나가다가 **가장 먼저** 조건을 만족한 WHEN 절과 대응하는  
THEN 절 식의 처리결과를 CASE 문의 결괏값으로 반환합니다.  
그 어떤 조건식도 만족하지 못한 경우에는 ELSE 절에 기술한 식이 채택됩니다. ELSE는 생략 가능하며 생략했을 경우 'ELSE NULL'로 간주됩니다.  
  
`SELECT a, CASE WHEN a IS NULL THEN 0 ELSE a END "a(null=0)" FROM sample37;`  
a 열 값이 NULL일 때 WHEN a IS NULL은 참이 되므로 CASE문은 THEN 절의 0을 반환합니다. NULL이 아닌 경우에는 ELSE절의 'a', 즉 a 열의 값을 반환합니다.  
  
- COALESCE
사실 NULL 값을 변환하는 경우라면 COALESCE 함수를 사용하는 편이 더 쉽습니다.  
`SELECT a, COALESCE(a,0) FROM sample37;`  
COALESCE 함수는 주어진 인수 가운데 NULL이 아닌 값에 대해서는 가장 먼저 지정된 인수의 값을 반환합니다.  
앞의 예문은 a가 NULL이 아니면 a값을 그대로 출력하고, 그렇지 않으면(a가 NULL이면) 0을 출력합니다.  
  
## 2. 또 하나의 CASE문
숫자로 이루어진 코드를 알아보기 더 쉽게 문자열로 변환하고 싶은 경우 CASE 문을 많이 사용합니다.  
문자화하는 것을 '디코드'라 부르고 반대로 수치화하는 것을 '인코드'라 부릅니다.  
  
다음은 SQL로 디코드를 하는 예시입니다.  
```SQL
WHEN a = 1 THEN '남자'
WHEN a = 2 THEN '여자'
```
CASE 문은 '검색 CASE'와 '단순 CASE'의 두 개 구문으로 나눌 수 있습니다.  
검색 CASE - 'CASE WHEN 조건식 THEN 식'  
단순 CASE - 'CASE 식 WHEN 식 THEN 식'. 단순 CASE에서는 CASE 뒤에 식을 기술하고 WHEN 뒤에 (조건식이 아닌) 식을 기술합니다.  
```SQL
CASE 식1
  WHEN 식2 THEN 식3
  WHEN 식4 THEN 식5
  ELSE 식6
END
```
식1의 값이 WHEN의 식2의 값과 동일한지 비교하고, 값이 같다면 식3의 값이 CASE문 전체의 결괏값이 됩니다.  
값이 같지 않으면 그 뒤에 기술한 WHEN절과 비교하는 식으로 진행됩니다.  
비교 결과 일치하는 WHEN 절이 하나도 없는 경우에는 ELSE 절이 적용됩니다.  
  
```SQL
SELECT a AS "코드",
CASE a
  WHEN 1 THEN '남자'
  WHEN 2 THEN '여자'
  ELSE '미지정'
END AS "성별" FROM sample37;
```
  
## 3. CASE 사용할 경우 주의사항
CASE문은 어디에서나 사용할 수 있습니다. (WHERE구에서 조건식의 일부, ORDER BY구나 SELECT 구 등)  
ELSE를 생략하면 ELSE NULL이 되는 것에 주의합시다. 상정한 것 이외의 데이터가 들어오는 경우도 많습니다.  
ELSE 생략 시 상정한 것 이외의 데이터가 왔을 때 NULL이 반환됩니다. 따라서 생략하지 않고 지정하는 편이 낫습니다.  
  
`WHEN NULL THEN '데이터 없음'`과 같이 지정하면 정상적으로 처리되지 않습니다.  
```SQL
SELECT a AS "코드",
CASE a
  WHEN 1 THEN '남자'
  WHEN 2 THEN '여자'
  ELSE '미지정'
END AS "성별" FROM sample37;
```
이 예제에서는 다음과 같은 순서로 조건식을 처리합니다.  
1. a=1  
2. a=2  
3. a=NULL  
비교 연산자 =로는 NULL 값과 같은지 아닌지를 비교할 수 없습니다. 따라서 a열의 값이 NULL이라 해도 a = NULL은 참이 되지 않습니다.  
즉 '데이터 없음'대신 '미지정'이라는 결괏값이 나옵니다.  
이때 IS NULL을 사용합니다. 다만 단순 CASE 문은 특성상 = 연산자로 비교하는 만큼, NULL 값인지를 판정하려면 검색 CASE문을 사용해야 합니다.  
  
단순 CASE문으로는 NULL 값을 비교할 수 없습니다.  
```SQL
SELECT a AS "코드",
CASE 
  WHEN a = 1 THEN '남자'
  WHEN a = 2 THEN '여자'
  WHEN a IS NULL THEN '데이터 없음'
  ELSE '미지정'
END AS "성별" FROM sample37;
```
  
NULL 값을 변환할 때는 표준 SQL로 규정되어 있는 COALESCE 함수를 사용합니다.  
