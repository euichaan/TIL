## 1. 자바, JVM, JDK, JRE
![](https://kangwoojin.github.io/assets/images/java/jdk-jre-jvm.png)

### JVM(Java Virtual Machine)
- 자바 가상 머신으로 자바 바이트 코드(.class 파일)를 OS에 특화된 코드로 변환(인터프리터와 JIT 컴파일러)하여 실행한다.  
- 바이트 코드를 실행하는 표준(JVM 자체는 표준)이자 구현체(특정 밴더가 구현한 JVM)다.  
- JVM 밴더: 오라클, 아마존, Azul...  
- 특정 플랫폼에 종속적  
  
### JRE(Java Runtime Environment): JVM + Library
- 자바 애플리케이션을 실행할 수 있도록 구성된 배포판.  
- JVM과 핵심 라이브러리 및 자바 런타임 환경에서 사용하는 프로퍼티 세팅이나 리소스 파일을 가지고 있다.  
- 개발 관련 도구는 포함하지 않는다. (그건 JDK에서 제공)  
  
### JDK(Java Development Kit): JRE + 개발 툴 
- JRE + 개발에 필요한 툴  
- 소스 코드를 작성할 때 사용하는 자바 언어는 플랫폼에 독립적.  
- 오라클은 자바 11부터는 JDK만 제공하며 JRE를 따로 제공하지 않는다.  
  
### 자바
- 프로그래밍 언어  
- JDK에 들어있는 자바 컴파일러(javac)를 사용하여 바이트코드(.class 파일)로 컴파일 할 수 있다.  
  
### Java Code 실행 과정
![](https://velog.velcdn.com/images/minseojo/post/6d345e14-4ea1-4c5f-8903-852627514512/image.PNG)  


