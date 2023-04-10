# HTTP 헤더 2 : 캐시와 조건부 요청  

## 캐시가 없을 때 - 첫 번째 요청  
![캐시가 없을 때 1](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-3.jpg)  
![캐시가 없을 때 2](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-4.jpg)  
## 캐시가 없을 때 - 두 번째 요청
![캐시가 없을 때 3](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-5.jpg)  
![캐시가 없을 때 4](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-6.jpg)  

- 데이터가 변경되지 않아도 계속 네트워크를 통해 데이터를 다운로드 받아야 한다  
- 인터넷 네트워크는 매우 느리고 비싸다  
- 브라우저 로딩 속도가 느리다 -> 느린 사용자 경험  

## 캐시 적용 - 첫 번째 요청
![캐시가 있을 때 1](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-8.jpg)  
![캐시가 있을 때 2](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-9.jpg)  
## 캐시 적용 - 두 번째 요청  
![캐시가 있을 때 3](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-10.jpg)  
![캐시가 있을 때 4](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-11.jpg)    

- 캐시 덕분에 캐시 가능 시간동안 네트워크를 사용하지 않아도 된다  
- 비싼 네트워크 사용량을 줄일 수 있다  
- 브라우저 로딩 속도가 매우 빠르다 -> 빠른 사용자 경험  

## 캐시 적용 - 세 번째 요청 : 캐시 시간 초과  
- 캐시 유효 시간이 초과하면, 서버를 통해 데이터를 다시 조회하고, 캐시를 갱신한다  
- 이때 다시 네트워크 다운로드가 발생한다  

## `캐시 시간 초과`
- 캐시 유효 시간이 초과해서 서버에 다시 요청하면 다음 두 가지 상황이 나타난다  
1. 서버에서 기존 데이터를 변경함  
2. 서버에서 기존 데이터를 변경하지 않음  

### 서버에서 기존 데이터를 변경하지 않은 경우  
- 데이터를 전송하는 대신에 저장해 두었던 캐시를 재사용 할 수 있다  
- 단, 클라이언트의 데이터와 서버의 데이터가 같다는 사실을 확인할 수 있는 방법 필요  

1. 첫 번째 응답 시 검증 헤더 추가 예)Last-Modified: 2020년 11월 10일 10:00:00(데이터가 마지막에 수정된 시간)  
2. 웹 브라우저의 브라우저 캐시에 응답 결과를 캐시에 저장한다(데이터 최종 수정일 저장)  
3. 두 번째 요청 시 캐시 시간이 초과했을 때, GET으로 요청 시 캐시가 가지고 있는 데이터 최종 수정일을 함께 보낸다  
```http
GET /star.jpg
if-modified-since: 2020년 11월 10일 10:00:00
```
4. 서버에서는 데이터 최종 수정일을 비교해 보고, 데이터가 아직 수정되지 않았음을 알 수 있다  
**5. 서버에서 HTTP Body는 전송하지 않고 헤더만 HTTP 헤더만 전송한다**  
```http
HTTP/1.1 304 Not Modified
...
Last-Modified: 2020년 11월 10일 10:00:00
```
6. 웹 브라우저에는 응답 결과를 재사용해 헤더 데이터를 갱신한다  
7. 웹 브라우저는 데이터를 브라우저 캐시에서 조회한다

![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-20.jpg)
![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-21.jpg)
![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-22.jpg)
![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-23.jpg)
![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-24.jpg)
![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-25.jpg)
![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-26.jpg)
![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-27.jpg)
![캐시와 조건부 검증](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-28.jpg)

### `검증 헤더와 조건부 요청 정리 : 검증 헤더(Last-Modified), 조건부 요청(if-modified-since)`
- 캐시 유효 시간이 초과해도, 서버의 데이터가 갱신되지 않으면 304 Not Modified + 헤더 메타 정보만 응답(바디X)  
- 클라이언트는 서버가 보낸 응답 헤더 정보로 캐시의 메타 정보를 갱신  
- 클라이언트는 캐시에 저장되어 있는 데이터 재활용  
- 결과적으로 네트워크 다운로드가 발생하지만 용량이 적은 헤더 정보만 다운로드  
- 매우 실용적인 해결책   

## 검증 헤더와 조건부 요청 2  
**검증 헤더**  
- 캐시 데이터와 서버 데이터가 같은지 검증하는 데이터  
- Last-Modified, ETag  

**조건부 요청 헤더**  
- 검증 헤더로 조건에 따른 분기  
- If-Modified-Since: Last-Modified 사용  
- If-None-Match: ETag 사용  
- 조건이 만족하면 200 OK  
- 조건이 만족하지 않으면 304 Not Modified  

**If-Modified-Since: 이후에 데이터가 수정되었으면?**    
데이터 미변경 예시  
- 캐시: 2020년 11월 10일 10:00:00 vs 서버: 2020년 11월 10일 10:00:00  
- 304 Not Modified, 헤더 데이터만 전송(BODY 미포함)  
- 전송 용량 0.1M(헤더 0.1M)

데이터 변경 예시  
- 캐시: 2020년 11월 10일 10:00:00 vs 서버: 2020년 11월 10일 11:00:00  
- 200 OK, 모든 데이터 전송(BODY 포함)  
- 전송 용량 1.1M(헤더 0.1M,바디 1.0M)  

**Last-Modified, If-Modified-Since 단점**  
- 1초 미만(0.x초) 단위로 캐시 조정이 불가능  
- 날짜 기반의 로직 사용  
- 데이터를 수정해서 날짜가 다르지만, 같은 데이터를 수정해서 데이터 결과가 똑같은 경우  
- 서버에서 별도의 캐시 로직을 관리하고 싶은 경우 예)스페이스나 주석처럼 크게 영향이 없는 변경에서 캐시를 유지하고 싶은 경우  

## ETag,If-None-Match  
ETag(Entity Tag)  
- 캐시용 데이터에 임의의 고유한 버전 이름을 달아둠 예)ETag: "v1.0", ETag: "a2jjodwjekl3"  
- 데이터가 변경되면 이 이름을 바꾸어서 변경함(Hash를 다시 생성)  
- 진짜 단순하게 ETag만 보내서 같으면 유지, 다르면 다시 받기  
- 캐시 제어 로직을 서버에서 완전히 관리  
- 클라이언트는 단순히 이 값을 서버에 제공(클라이언트는 캐시 메커니즘을 모름)  
- 예)서버는 배타 오픈 기간인 3일 동안 파일이 변경되어도 ETag 동일하게 유지, 배포 주기에 맞추어 ETag 모두 갱신  

## Using ETag  
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-35.jpg)
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-36.jpg)
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-37.jpg)
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-38.jpg)
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-39.jpg)
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-40.jpg)
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-41.jpg)
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-42.jpg)
![ETag 사용한 전송](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-43.jpg)

- 웹 브라우저에서 보낸 ETag와 서버에서 보낸 ETag 가 같다 -> data가 아직 수정되지 않았다.  

## 캐시 제어 헤더
### Cache-Control: 캐시 제어
- Cache-Control: max-age(캐시 유효 시간, 초 단위)  
- Cache-Control: no-cache(데이터는 캐시해도 되지만, 항상 원(origin)서버에 검증하고 사용)  
- Cache-Control: no-store(데이터에 민감한 정보가 있으므로 저장하면 안됨.메모리에서 사용하고 최대한 빨리 삭제)  
- Cache-Control: public(응답이 public 캐시에 저장되어도 됨)  
- Cache-Control: private(응답이 해당 사용자만을 위한 것임,private 캐시에 저장해야 함-기본값)
- Cache-Control: s-maxage(프록시 캐시에만 적용되는 max-age)  
- Age:60(오리진 서버에서 응답 후 프록시 캐시 내에 머문 시간(초))  

### Pragma: 캐시 제어(하위 호환)
- Pragma: no-cache  
- HTTP 1.0 하위 호환  

### Expires: 캐시 유효 기간(하위 호환).캐시 만료일 지정
- 예)expires: Mon,01 Jan 1990 00:00:00 GMT  
- 캐시 만료일을 정확한 날짜로 지정  
- HTTP 1.0 부터 사용  
- Cache-Control: max-age 권장  
- Cache-Control: max-age와 함께 사용하면 Expires는 무시  

## 프록시 캐시  
![http 헤더 - 프록시 캐시](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-52.jpg)  
![http 헤더 - 프록시 캐시](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-53.jpg)  
![http 헤더 - 프록시 캐시](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-54.jpg)  
- 웹 브라우저는 private 캐시, 프록시 캐시 서버는 public 캐시 사용  

## 캐시 무효화
- Cache-Control: no-cache, no-store, must-revalidate  
- Pragma: no-cache

### Cache-Control : 캐시 지시어 - 확실한 캐시 무효화  
- Cache-Control: no-cache(데이터는 캐시해도 되지만, 항상 원(origin)서버에 검증하고 사용)  
- Cache-Control: no-store(데이터에 민감한 정보가 있으므로 저장하면 안됨.메모리에서 사용하고 최대한 빨리 삭제)  
- Cache-Control: must-revalidate(캐시 만료후 최초 조회시 원 서버에 검증해야 함.원 서버 접근 실패시 반드시 오류 발생-504 Gateway Timeout)   
- Pragma: no-cache: HTTP 1.0 하위 호환  

### no-cache vs must-revalidate
![no-cache vs must-revalidate](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-59.jpg)  
![no-cache vs must-revalidate](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-60.jpg)  
![no-cache vs must-revalidate](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-61.jpg)  
![no-cache vs must-revalidate](https://github.com/euichanhwang/CS_study/blob/main/img/8.http-header2.pdf-62.jpg)  

no-cache: 원 서버에 접근할 수 없는 경우 서버 설정에 따라서 캐시 데이터 반환 가능. Error or 200 OK(오류 보다는 오래된 데이터 보여주자)  
must-revalidate: 원 서버에 접근할 수 없는 경우, 항상 오류 발생. 504 Gateway Timeout  

