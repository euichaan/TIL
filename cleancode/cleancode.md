# Clean Code 
## 10장 클래스 체계 
클래스를 정의하는 표준 자바 관례  
1. public static 상수  
2. private static 변수  
3. 비공개 인스턴스 변수  
4. 공개 함수  
5. 비공개 함수는 자신이 호출하는 공개 함수 직후에 넣는다.  

Standard Java Convention에는 다음과 같이 적혀있다.  
1. 클래스/인터페이스 주석  
2. 클래스/인터페이스 문   
3. 클래스/인터페이스 구현 주석 (필요한 경우)  
4. 클래스(static) 변수들 - public, protected, package-private, private 순서  
5. 인스턴스 변수들 - public, protected, package-private, private 순서  
6. 생성자  
7. 메서드들 - private 메서드가 public 메서드 사이에 올 수 있다. 목표는 가독성이다.  