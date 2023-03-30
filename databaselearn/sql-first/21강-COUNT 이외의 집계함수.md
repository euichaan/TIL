# 21강 - 카운트 이외의 집계함수
SUM([ALL|DISTINCT] 집합)  
AVG([ALL|DISTINCT] 집합)  
MIN([ALL|DISTINCT] 집합)  
MAX([ALL|DISTINCT] 집합)  
  
## 1. SUM으로 합계 구하기
SELECT SUM(quantity) FROM sample51;  
SUM 집계함수에 지정되는 집합은 수치형 뿐입니다.  
묹자열형이나 날짜시간형의 집합에서 합계를 구할 수는 없습니다.  
name 열은 문자열형이므로 SUM(name) 과 같이 지정할 수는 없습니다.  
한편, SUM 집계함수도 COUNT와 마찬가지로 NULL 값을 무시합니다. NULL 값을 제거한 뒤에 합계를 냅니다.  
  
## 2. AVG로 평균내기
합한 값을 개수로 나누면 평균값을 구할 수 있습니다.  
SUM(quantity) / COUNT(quantity)와 같이 지정하면 됩니다.  
  
AVG라는 집계함수를 통해 평균값을 간단하게 구할 수 있습니다.  
AVG 집계함수에 주어지는 집합은 SUM과 동일하게 수치형만 가능합니다.  
`SELECT AVG(quantity), SUM(quantity)/COUNT(quantity) FROM sample51;`  
AVG 집계함수도 NULL 값은 무시합니다. 즉, NULL 값을 제거한 뒤에 평균값을 계산합니다.  
  
만약 NULL을 0으로 간주해서 평균을 내고 싶다면 CASE를 사용해 NULL을 0으로 변환한 뒤에 AVG 함수로 계산하면 됩니다.  
`SELECT AVG(CASE WHEN quantity IS NULL THEN 0 ELSE quantity END) AS avgnull0 FROM sample51;`  
  
## 3.MIN, MAX로 최솟값,최댓값 구하기
MIN 집계함수, MAX 집계함수를 사용해 집합에서 최솟값과 최댓값을 구할 수 있습니다.  
이들 함수는 문자열형과 날짜시간형에도 사용할 수 있습니다. 다만 NULL값을 무시하는 기본규칙은 다른 집계함수와 같습니다.  
`SELECT MIN(quantity), MAX(name) from sample51;`
