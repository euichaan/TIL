# GC(Garbage Collection)
GC는 메모리 관리 기법 중 하나로, 동적으로 할당했던 메모리 영역 중 필요 없게 된 영역을 해제하는 기능이다.  
여기서 동적으로 할당했던 메모리 영역은 프로그램 런타임에 사용되는 Heap 영역 메모리를 뜻하고, 필요 없게 된 영역은 어떤 변수도 가리키지 않게 된 영역을 의미한다.  
  
## GC의 장점
개발자의 실수로 인한 메모리 누수를 막을 수 있고, 해제된 메모리에 접근하는 오류와 해제된 메모리를 또 해제하는 이중 해제 또한 막을 수 있다.  
  
## GC의 단점 
어떠한 메모리 영역이 해제의 대상이 될지 검사하고 해제하는 일은 순수 오버헤드이다.  
또한 GC의 메모리 해제 타이밍을 개발자가 정확하게 알기 어렵다.  
  
## GC 알고리즘 - Reference Counting
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/08c34f7f-54c6-43f2-ad16-9dcc31752dd9)
  
그림 속 Root Space는 스택 변수, 전역 변수 등 heap 영역 참조를 들고 있는 공간이다.  
  
Reference Counting은 힙 영역의 객체들이 각각 reference count라는 숫자를 가지고 있다고 생각하면 좋다.  
여기서 reference count는 **몇 가지 방법으로 해당 객체에 접근할 수 있는지를 뜻한다.**    
reference count가 0에 다다르면, 즉 해당 객체에 접근할 수 있는 방법이 하나도 없다면 가비지 컬렉션의 대상이 된다.  
  
reference counting에는 한계점이 있는데 바로 순환 참조 문제이다.  
그림 속 Root space에서의 Heap space 접근을 모두 끊는다고 생각해보면 노란색 고리 안의 객체들은 서로가 서로를 참조하고 있기 때문에 Reference count가 1로 유지된다.  
  
결국 사용하지 않는 메모리 영역이 해제되지 못하고 Memory Leak이 발생한다.  
## GC 알고리즘 - Mark And Sweep
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/34c91132-d724-4a99-8e27-ed656fe339a8)
  
Mark And Sweep 알고리즘은 Reference Counting의 순환 참조 문제를 해결할 수 있다.  
  
Mark And Sweep은 **루트에서부터 해당 객체에 접근 가능한지, 아닌지를 해제의 기준으로 삼는다.**  
루트부터 그래프 순회를 통해 연결된 객체들을 찾아내고(Mark),  
연결이 끊어진 객체들은 지우는 방식이다.(Sweep)  
  
루트로부터 연결된 객체는 Reachable, 연결되지 않았다면 Unreachable이라고 불린다.  
  
그림에서는 Sweep 이후에 분산되어 있던 메모리가 정리된 것을 볼 수 있는데, 이를 메모리 파편화를 막는 Compaction이라고 한다. 다만, Mark And Sweep에서 Compaction이 필수는 아니다.  
  
이렇게 Mark And Sweep 방식을 사용하면, **루트로부터 연결이 끊긴 순환 참조되는 객체들**도 지울 수 있다.  
Java와 JavaScript가 Mark And Sweep 방식으로 메모리 관리를 한다.  
  
Mark And Sweep도 단점이 있는데, 의도적으로 특정 순간에 GC를 실행시켜야 한다.  
즉 어느 순간에는 실행 중인 애플리케이션이 GC에게 컴퓨터 리소스들을 내줘야 한다.  
  
Mark And Sweep 방식의 특징을 정리하면 다음과 같다.  
- 의도적으로 GC를 실행시켜야 한다  
- 애플리케이션 실행과 GC 실행이 병행된다  
  
## JVM에서의 Root Space
Mark And Sweep 알고리즘은 Root Space로부터 동적 메모리 영역의 객체 접근이 가능한지를 해제의 기준으로 둔다.  
Root Space에서부터 해당 객체에 접근이 가능하다면 메모리에 유지시키고, 접근이 불가능하면 메모리에서 지워버리는 방식이다.  
  
JVM의 Root Space는 어디일까? Heap 영역 메모리에 대한 참조를 들고 있을 수 있는 영역일 것이다.  
1. Stack의 로컬 변수  
2. Method Area의 Static 변수  
3. Native Method Stack의 JNI 참조  
  
## GC 실행의 타이밍 
Mark And Sweep의 첫 번째 특징은 "의도적으로 GC를 실행시켜야 한다"이다.  
JVM GC는 언제 동작하는 것일까?  
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/63507d30-5ceb-4b49-ae80-70f6f3cb0a36)
  
JVM의 Heap 영역은 크게 두 영역, Young Generation과 Old Generation으로 나뉜다.  
Young Generation에서 발생하는 GC는 minor gc, Old Generation에서 발생하는 GC는 major gc라고 불린다.  
Young Generation은 또 다시 세 영역으로 나뉜다.  
- Eden  
- Survival 0  
- Survival 1  
  
Eden은 새롭게 생성된 객체들이 할당되는 영역이다.  
Survival 영역은 minor gc로부터 살아남은 객체들이 존재하는 영역이다.  
Survival 0 혹은 Survival 1 둘 중 하나는 꼭 비어 있어야 한다.  
  
minor gc의 실행 타이밍은 바로 Eden 영역이 꽉 찼을 때이다.  
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/2f041202-0427-4d92-99fc-0b293922452f)
  
minor gc가 발생하고 난 뒤 Reachable이라 판단된 객체들은 아래 그림과 같이 Survival 0 영역으로 옮겨진다.  
살아남은 객체들의 숫자들이 0에서 1로 변한 것을 알 수 있는데, 이는 age bit을 뜻한다.  
Minor gc에서 살아남을 때마다 1씩 증가한다.  
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/70fc2d7d-8fd4-44e4-a9c2-aadd9a212df0)
  
또다시 Eden 영역이 꽉 차면, minor gc가 발생한다.  
이번에 Reachable이라 판단된 객체들은 Survival 1으로 이동한다.  
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/415774db-fe08-4b37-83e0-2b56b9efa03c)  
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/bcbbeba7-ce35-43d8-9e58-d09fa0cc38a1)
  
이후 또 Eden 영역이 꽉 차면, minor gc가 발생하고 이번에는 Survival 0으로 이동할 것이다.  
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/118f83c3-fb32-4219-a1f8-11c5808eac15)  
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/9614d9ca-f355-4f7f-a350-53fbdae4900b)
  
Survival 0 영역으로 넘어온 객체 중 하나가 age bit가 3이 되었다.  
JVM GC에서는 일정 수준의 age-bit를 넘어가면 오래도록 참조될 객체구나 라고 판단하고, 해당 객체를 Old Generation에 넘겨준다. 이 과정을 Promotion이라고 한다.  
Java 8에서는 Parallel GC 방식 사용 기준 age bit가 15가 되면 promotion이 진행된다.  
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/5492f3de-6828-404a-94a7-a59cda05e6e2)
  
언젠가 Old generation도 다 채워질 때 Major GC가 발생하면서 Mark And Sweep 방식을 통해 필요없는 메모리를 비운다. Major GC는 Minor GC보다 더 오래 걸리게 된다.  
  
## GC 실행 방식 
Mark And Sweep의 두번째 특징, "애플리케이션 실행과 GC 실행이 병행된다"는 말은 곧  
**JVM에서는 애플리케이션과 GC를 병행하여 실행할 수 있는 여러 옵션들을 제공한다는 말이다.**    
  
Stop The World란 GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것이다.  
  
### Serial GC
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/26c55cc1-ec75-4699-b1a0-6e028a8f846a)
  
하나의 쓰레드로 GC를 실행하는 방식. 하나의 쓰레드로 GC를 실행시키다 보니 Stop-The-World 시간이 긴 것을 알 수 있다. 싱글 쓰레드 환경 및 Heap 영역이 매우 작을 때 사용하기 위해 나온 방식이다.  
### Parallel GC
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/8b00b370-2edc-4815-bf96-aa8a1fb579a7)
  
여러 개의 쓰레드로 가비지 컬렉션을 수행한다. 앞선 Serial GC보다 Stop The World 시간이 짧아진 것을 알 수 있다. 멀티코어 환경에서 애플리케이션 처리 속도를 향상시키기 위해 사용된다.  
Java 8에서 기본으로 쓰이는 GC 방식이다.  
### CMS GC
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/5d349d80-8cb9-4337-bed1-05fc7c1f7c1e)
  
CMS는 Concurrent-Mark-Sweep의 줄임말이다.  
Stop-The-World 시간을 최소화하기 위해 고안됐다.  
대부분의 가비지 수집 작업을 애플리케이션 스레드와 동시에 수행하여, Stop The World 시간을 최소화시킨다.  
하지만 메모리와 CPU를 많이 사용하고, Mark-And-Sweep 과정 이후 메모리 파편화를 해결하는 Compaction이 기본적으로 제공되지 않는다.  
### G1 GC
![image](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/82807f4e-13d9-4863-a0c4-e88b85e78c39)
  
G1 은 가비지 퍼스트의 줄임말이다.  
G1 GC는 Heap 영역을 조금 다르게 사용한다. Heap을 일정 크기의 Region으로 잘게 나누어 어떤 영역은 Young Gen, 어떤 영역은 Old Gen으로 쓴다. 런타임에 G1 GC가 필요에 따라 영역별 Region 개수를 튜닝한다. 그에 따라 Stop the world를 최소화 할 수 있었다고 한다.  
  
Java 9 이상부터는 G1 GC를 기본 GC 실행방식으로 사용한다.  
