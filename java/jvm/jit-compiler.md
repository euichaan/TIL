# Understanding JIT(Just-in-time compiler)
[Understanding JIT(Just-in-time compiler](https://aboullaite.me/understanding-jit-compiler-just-in-time-compiler/)  
  
JIT(Just-In-Time) 컴파일러는 런타임에 Java 응용 프로그램의 성능을 향상시키는 JRE(Java Runtime Environment)의 구성 요소입니다. JVM에서 컴파일러보다 성능에 더 영향을 미치는 것은 없으며 컴파일러 선택은 Java 개발자든 최종 사용자든 Java 애플리케이션을 실행할 때 가장 먼저 내리는 결정 중 하나입니다.  
  
## Java JIT Compiler : 개요
"Write once, run anywhere" 자바 파워의 핵심은 바이트코드입니다. 바이트코드가 응용 프로그램에 대한 적절한 native instruction으로 변환되는 방식은 응용 프로그램 속도에 큰 영향을 미칩니다.  
    
이러한 바이트코드는 해석되거나(interpreted), navitve code로 컴파일되거나 명령어 세트 아키텍처(Instruction Set Architecture)가 바이트코드 사양인 프로세서에서 직접 실행될 수 있습니다. JVM(Java Virtual Machine)의 표준 구현인 바이트코드를 해석하면(Interpreting the bytecode) 프로그램 실행 속도가 느려집니다.  
    
성능을 향상시키기 위해 JIT 컴파일러는 런타임에 JVM과 상호 작용하고 적절한 바이트 코드 시퀀스(bytecode sequence)를 native machine code(기본 기계 코드)로 컴파일합니다.  
  
JIT 컴파일러를 사용할 때 하드웨어는 JVM이 동일한 바이트 코드 시퀀스를 반복적으로 해석하고 상대적으로 긴 번역 프로세스의 페널티를 초래하는 것과는 반대로 네이티브 코드를 실행할 수 있습니다.이것은 메서드가 덜 자주 실행되지 않는(많이 실행되는 한) 한 실행 속도의 성능 향상으로 이어질 수 있습니다.  
  
JIT 컴파일러가 바이트코드를 컴파일하는 데 걸리는 시간은 전체 실행 시간에 추가되며, JIT에 의해 컴파일된 메서드가 자주 호출되지 않는 경우 바이트코드를 실행하는 인터프리터보다 실행 시간이 더 길어질 수 있습니다.  
  
JIT 컴파일러는 byte code(바이트 코드)를 native code(네이티브 코드)로 컴파일할 때 특정 최적화를 수행합니다.  
JIT 컴파일러는 일련의 바이트코드를 native instructions(기본 명령어)로 변환하므로 몇 가지 간단한 최적화를 수행할 수 있습니다.  
   
JIT 컴파일러가 수행하는 일반적인 최적화 중 일부는 데이터 분석, 스택 작업에서 레지스터 작업으로의 변환, 레지스터 할당에 의한 메모리 액세스 감소, 공통 하위 표현식 제거 등입니다.(data-analysis, translation from stack operations to register operations, reduction of memory accesses by register allocation, elimination of common sub-expressions etc)  
JIT 컴파일러가 수행하는 최적화 수준이 높을수록 실행 단계에서 더 많은 시간을 소비합니다.  
따라서 JIT 컴파일러는 실행 시간에 추가되는 오버헤드와 프로그램에 대한 제한된 뷰만 있기 때문에 정적 컴파일러가 수행하는 모든 최적화를 수행할 수 없습니다.  
  
제한된 뷰(restricted view): 제한된 보기는 특정 유리한 지점에서 제한된 또는 부분적으로 가려진 관점을 나타냅니다.  
  
## How does it work?
Java 프로그램은 다양한 컴퓨터 아키텍처에서 JVM이 해석할 수 있는 플랫폼 중립 바이트코드를 포함하는 클래스로 구성됩니다. 런타임에 JVM은 클래스 파일을 로드하고 각 개별 바이트 코드의 의미 체계를 결정하고 적절한 계산을 수행합니다.  
  
해석 중 추가 프로세서 및 메모리 사용은 Java 애플리케이션이 기본 애플리케이션보다 더 느리게 수행됨을 의미합니다.  
  
JIT 컴파일러는 런타임에 바이트코드를 원시 기계 코드로 컴파일하여 Java 프로그램의 성능을 향상시키는 데 도움이 됩니다.(The JIT compiler helps improve the performance of Java programs by compiling bytecode into native machine code at run time.)  
at Runtime, bytecode -> native machine code  
![](https://aboullaite.me/content/images/2017/08/jit.png)  
JIT 컴파일러는 기본적으로 활성화되어 있으며 Java 메서드가 호출될 때 활성화됩니다.  
JIT 컴파일러는 해당 메서드의 바이트 코드를 네이티브 머신 코드로 컴파일하여 "적시에"(just in time) 실행하도록 컴파일합니다.  
메소드가 컴파일되면 JVM은 해당 메소드의 컴파일된 코드를 해석하는 대신 직접 호출합니다.  
  
이론적으로 컴파일에 프로세서 시간과 메모리 사용이 필요하지 않은 경우 모든 메서드를 컴파일하면 Java 프로그램의 속도가 네이티브 애플리케이션의 속도에 접근할 수 있습니다. JIT 컴파일에는 프로세서 시간과 메모리 사용량이 필요합니다. JVM이 처음 시작되면 수천 개의 메소드가 호출됩니다. 이러한 메서드를 모두 컴파일하면 프로그램이 결국 매우 우수한 최고 성능을 달성하더라도 시작 시간에 상당한 영향을 미칠 수 있습니다.  
