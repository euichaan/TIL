# 인터넷 네트워크
## IP 주소 부여
- 지정한 IP 주소(IP Address)에 데이터 전달  
- 패킷(Packet)이라는 통신 단위로 데이터 전달  
![IP 패킷 정보](https://github.com/euichanhwang/CS_study/blob/main/img/1.internet-network.pdf-12.jpg)  

## 클라이언트 패킷 전달 예시
출발 100.100.100.1  
목적 200.200.200.2  
Hello,world!(전송 데이터)  
...  

## 서버 패킷 전달 예시
출발 200.200.200.2  
목적 100.100.100.1  
OK  
...  

## `IP 프로토콜의 한계`
비연결성  
- 패킷을 받을 대상이 없거나 서비스 불능 사태여도 패킷 전송  

비신뢰성  
- 중간에 패킷이 사라지거나 순서대로 오지 않는 경우는?(패킷 소실)    

프로그램 구분  
- 같은 IP를 사용하는 서버에서 통신하는 애플리케이션이 둘 이상이면?  

# TCP/UDP
## 인터넷 프로토콜 스택의 4계층
애플리케이션 계층 - HTTP,FTP  
전송 계층 - TCP,UDP  
인터넷 계층 - IP  
네트워크 인터페이스 계층  
![프로토콜 계층](https://github.com/euichanhwang/CS_study/blob/main/img/1.internet-network.pdf-22.jpg)  
![프로토콜 계층](https://github.com/euichanhwang/CS_study/blob/main/img/1.internet-network.pdf-23.jpg)  

## TCP/IP 패킷 정보  
![프로토콜 계층](https://github.com/euichanhwang/CS_study/blob/main/img/1.internet-network.pdf-25.jpg)  

## `TCP 특징`  
전송 제어 프로토콜(Transmission Control Protocol)  
**연결지향 - TCP 3 way handshake(가상 연결)**    
![TCP 3 way handshake](https://github.com/euichanhwang/CS_study/blob/main/img/1.internet-network.pdf-27.jpg)  

**데이터 전달 보증**    
1. 데이터 전송(클라이언트->서버)  
2. 데이터 잘 받았음(서버->클라이언트)  

**순서 보장**    
1. 패킷1,패킷2,패킷3 순서로 전송(클라이언트->서버)  
2. 서버에 패킷1,패킷3,패킷2 순서로 도착  
3. 패킷2부터 다시 보내라고 요청(서버->클라이언트)    

신뢰할 수 있는 프로토콜  
현재는 대부분 TCP 사용  

## UDP 특징
사용자 데이터그램 프로토콜(User Datagram Protocol)  
연결지향 X, 데이터 전달 보증 X, 순서 보장 X  
데이터 전달 및 순서가 보장되지 않지만, 단순하고 빠름  

정리
- IP와 거의 같다.+PORT+체크섬 정도만 추가  
- 애플리케이션에서 추가 작업 필요  

## `PORT`
비유
- IP: 목적지 서버를 찾음(아파트)  
- PORT: 서버 안에서 돌아가는 application 들을 구분(몇동 몇호)  

패킷 정보  
출발지 IP,PORT  
목적지 IP,PORT  
전송 데이터  
...  

**PORT- 같은 IP 내에서 프로세스 구분**  
0 ~ 65535: 할당 가능  
0 ~ 1023: 잘 알려진 포트, 사용하지 않는 것이 좋음  
FTP-20,21    
TELNET-23  
HTTP-80  
HTTPS-443  

## DNS(Domain Name System)
IP는 기억하기 어렵고, 변경될 수 있다.  
- 전화번호부
- 도메인 명을 IP 주소로 변환 
![DNS](https://github.com/euichanhwang/CS_study/blob/main/img/1.internet-network.pdf-42.jpg)  
1. 도메인 명을 입력 예)google.com  
2. 응답: 200.200.200.2(DNS 서버에서 응답)  
3. 접속: 200.200.200.2  







