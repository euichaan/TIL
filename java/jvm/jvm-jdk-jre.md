## 1. 자바, JVM, JDK, JRE
![](https://github.com/euichaan/TIL/blob/main/img/jdk%2Cjre%2Cjvm.png)

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

1. 개발자가 자바 소스코드(.java)를 작성  
2. 자바 컴파일러가 자바 소스코드(.java)파일을 읽어 바이트코드(.class)코드로 컴파일 합니다. 바이트코드(.class)파일은 아직 컴퓨터가 읽을 수 없는 JVM이 읽을 수 있는 코드입니다. (.java -> .class)  
3. 컴파일된 바이트코드(.class)를 JVM의 클래스로더(Class Loader)에게 전달합니다.  
4. 클래스 로더는 동적 로딩(Dynamic Loading)을 통해 필요한 클래스들을 로딩 및 링크하여 런타임 데이터 영역(Runtime Data Area의 Method Area), 즉 JVM의 메모리에 올립니다.  
5. 실행 엔진(Execution Engine)은 JVM 메모리에 올라온 바이트 코드들을 명령어 단위로 하나씩 가져와서 실행합니다. 이 때 실행 엔진은 두 가지 방식으로 변경합니다.  
  
실행 두 가지 방식  
1. **인터프리터**: 바이트 코드 명령어를 하나씩 읽어서 해석하고 실행합니다. 하나하나의 실행은 빠르나, 전체적인 실행 속도가 느리다는 단점을 가집니다.  
2. **JIT 컴파일러**: 런타임 시 바이트 코드를 원시 시스템 코드로 컴파일하여 Java 애플리케이션의 성능을 향상시키는 런타임 환경의 컴포넌트 입니다.  