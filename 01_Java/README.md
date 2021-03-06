# Java 질문

기본적으로는 [신입 개발자 면접 질문 시리즈](https://www.notion.so/54d624628a634c879cc93d94f54cd2d1)에서 답변을 가지고 왔습니다.

### JVM구조에 대해 설명하시오.

[JVM(Java Virtual Machine)](https://jeong-pro.tistory.com/148). 자바 가상 머신으로 자바 바이트 코드를 실행할 수 있는 주체다. CPU나 운영체제(플랫폼)의 종류와 무관하게 실행이 가능하다.즉, 운영체제 위에서 동작하는 프로세스로 자바 코드를 컴파일해서 얻은 바이트 코드를 해당 운영체제가 이해할 수 있는 기계어로 바꿔 실행시켜주는 역할을 한다.

JVM의 구성을 살펴보면 크게 4가지(Class Loader, Execution Engine, Garbage Collector, Runtime Data Area)로 나뉜다.

1. Class Loader
   1. 자바에서 소스를 작성하면 Person.java 처럼 .java파일이 생성된다.
   2. .java 소스를 자바컴파일러가 컴파일하면 Person.class 같은 .class파일(바이트코드)이 생성된다.
   3. 이렇게 생성된 클래스파일들을 엮어서 JVM이 운영체제로부터 할당받은 메모리영역인 Runtime Data Area로 적재하는 역할을 Class Loader가 한다. (자바 애플리케이션이 실행중일 때 이런 작업이 수행된다.)
2. Execution Engine
   1. Class Loader에 의해 메모리에 적재된 클래스(바이트 코드)들을 기계어로 변경해 명령어 단위로 실행하는 역할을 한다.
   2. 명령어를 하나 하나 실행하는 인터프리터(Interpreter)방식이 있고 JIT(Just-In-Time) 컴파일러를 이용하는 방식이 있다.
   3. JIT 컴파일러는 적절한 시간에 전체 바이트 코드를 네이티브 코드로 변경해서 Execution Engine이 네이티브로 컴파일된 코드를 실행하는 것으로 성능을 높이는 방식이다.
3. Garbage Collector
   1. Garbage Collector(GC)는 Heap 메모리 영역에 생성(적재)된 객체들 중에 참조되지 않는 객체들을 탐색 후 제거하는 역할을 한다.
   2. GC가 역할을 하는 시간은 정확히 언제인지를 알 수 없다. (참조가 없어지자마자 해제되는 것을 보장하지 않음)
   3. 또 다른 특징은 GC가 수행되는 동안 GC를 수행하는 쓰레드가 아닌 다른 모든 쓰레드가 일시정지된다.
   4. 특히 Full GC가 일어나서 수 초간 모든 쓰레드가 정지한다면 장애로 이어지는 치명적인 문제가 생길 수 있는 것이다. (GC와 관련된 내용은 아래 Heap영역 메모리를 설명할 때 더 자세히 알아본다.)
4. Runtime Data Area
   1. JVM의 메모리 영역으로 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 영역이다.
   2. 이 영역은 크게 Method Area, Heap Area, Stack Area, PC Register, Native Method Stack로 나눌 수 있다.

![](https://github.com/conquerex/OneHundredMillionSalary/blob/master/1_Java/image_jvm.png?raw=true)

<br>

### static은 메모리 구조 중 어디에 올라가나요?

[메모리의 공간](https://blog.naver.com/heartflow89/220954420688)은 크게 Static(스태틱) 영역, Stack(스택) 영역, Heap(힙) 영역으로 구분되고 데이터타입(자료형)에 따라서 해당 공간에 할당된다.

하나의 JAVA 파일은 크게 필드(field), 생성자(constructor), 메소드(method)로 구성된다. 그중 필드 부분에서 선언된 변수(전역변수)와 정적 멤버변수(static이 붙은 자료형) Static 영역에 데이터를 저장한다. Static 영역의 데이터는 프로그램의 시작부터 종료가 될 때까지 메모리에 남아있게 된다. 다르게 말하면 전역변수가 프로그램이 종료될 때까지 어디서든 사용이 가능한 이유이기도 하다. 따라서 전역변수를 무분별하게 많이 사용하다 보면 메모리가 부족할 우려가 있어 필요한 변수만 사용할 필요가 있다.
> 추가자료 : https://siyoon210.tistory.com/124

<br>

### GC처리 방법에 대해 설명하시오. (jdk 1.6 이하)
Garbage Collection, 줄여서 약어로 GC라고도 부른다. 영어로 그대로 읽어서 가비지 컬렉션이라고 부른다. 메모리 관리 방법 중에 하나로, 시스템에서 더이상 사용하지 않는 동적 할당된 메로리 블럭을 찾아 자동으로 다시 사용 가능한 자원으로 회수하는 것으로 시스템에서 가비지컬렉션을 수행하는 부분을 가비지 컬렉터라 부른다.

GC 작업을 하는 가비지 콜렉터는 다음과 같은 일을 한다.

1. 메모리 할당
2. 사용 중인 메모리 인식
3. 사용하지 않는 메모리 인식

즉, 메모리가 부족할 때 쓰레기를 정리해주는 프로그램을 가비지 컬렉터라고 부른다. 가비지 컬렉터에 대해서 알기 전에 우선 메모리에 대한 이해가 필요한데 프로그램을 실행할 때 메모리를 관리하는 OS에 프로그램 실행에 필요한 메모리를 요청하게 된다. 이때 메모리를 어디에 저장할지 그 주소를 할당하는데 이 주소를 offset 주소라고 부른다.

이 할당된 메모리들은 프로그램이 돌아가면 필연적으로 '가비지'가 발생하게 된다. 기존에 가리키고 있던 메모리를 새롭게 선언되거나 형변환이 되면서 다른 곳을 가리키게 되면서 주소를 잃어버리게 되고 다시 찾을 수 없게 되면서 정리되지 않은 메모리가 생겨버리게 되기 때문이다.

그래서 가비지 컬렉터는 가비지를 다른 용도로 사용할 수 있도록 메모리 해제를 시킨다. 이것이 가비지 컬렉터의 목적이다.
자바 기준으로 JVM은 메모리를 부여받고 프로그램을 실행하다가 메모리가 부족해지는 순간이 오면 추가적으로 메모리를 더 요청한다. 요청하는 바로 이때 가비지 컬렉터가 실행된다.

> 추가자료 : https://mirinae312.github.io/develop/2018/06/04/jvm_gc.html (헌진이 설명에 보조 자료)
https://d2.naver.com/helloworld/1329 (좋은 자료. 굳굳)

<br>

### Synchronize에 대해 설명하시오. Synchronize를 하기 위한 방법은 무엇이 있나요?
[멀티쓰레드](https://perfectacle.github.io/2019/03/10/java-synchronized-note/)를 사용하는 이유가 무엇일까? CPU가 놀고 있을 때 다른 쓰레드가 CPU를 점유한다면 CPU는 더 이상 놀지 않게 된다. 이렇게 CPU의 병목을 줄이다보면 성능을 개선할 수 있다. 또한 시분할 다중화를 통해 동시에 여러 작업이 처리되게 끔 보이게 할 때도 멀티쓰레드를 사용한다.

쓰레드는 프로세스 내부에 존재하기 때문에 프로세스 내부의 자원을 공유한다. 따라서 공유 자원에 대해서 동기화 이슈가 매우 중요하다. A 쓰레드의 작업이 완전히 끝나기 전에 A’라는 자원이 다른 쓰레드에 의해 값이 바뀌게 되면 A 쓰레드는 원하는 값을 얻어낼 수 없다.

위와 같은 문제를 해결하기 위해서는 synchronized 키워드를 사용하여 A 쓰레드의 작업이 끝날 때까지 대기해라!라고 명령을 내릴 수 있다. 더 나아가 synchronized 블럭 내에 있는 공유 자원을 점유하라!라고 이해를 하는 게 좀 더 정확하다.

CPU에서 명령을 수행하기 위해서는 메모리에 있는 데이터를 CPU로 가져와야한다. 하지만 메모리에 있는 데이터를 CPU로 가져오는 행위는 매우 느리므로 CPU는 캐시 메모리가 있다. (L1 캐시, L2 캐시 등등) 그리고 이 캐시 메모리를 쓰레드 로컬 변수라고 부른다.

하지만 쓰레드 로컬 변수이기 때문에 다른 쓰레드에서는 메모리에 접근을 해도 해당 쓰레드 변수의 값을 얻어올 수 없다.

여기서 synchronized 키워드가 가지는 진정한 의미가 나온다. synchronized 키워드를 사용한다는 것은 해당 블럭 내에 있는 공유 자원 A’가 쓰레드 로컬 변수에서 램으로 써지기 까지 다른 쓰레드는 대기(block)하라라는 의미를 가진다. 쓰레드 로컬 변수가 램에 써진 순간 다른 쓰레드가 램에서 해당 값을 가져와서 작업할 수 있게 된다.

> 사용방법 : https://parkcheolu.tistory.com/15
은행 시스템 예시 : https://velog.io/@zehye/%EC%93%B0%EB%A0%88%EB%93%9CThreads%EC%99%80-%EB%8F%99%EA%B8%B0%ED%99%94Synchronization

<br>

### Collection Framework 설명
다수의 데이터를 쉽고 효과적으로 처리할 수 있는 표준화된 방법을 제공하는 [클래스의 집합](http://tcpschool.com/java/java_collectionFramework_concept)을 의미합니다. 즉, 데이터를 저장하는 자료 구조와 데이터를 처리하는 알고리즘을 구조화하여 클래스로 구현해 놓은 것입니다. 이러한 컬렉션 프레임워크는 자바의 인터페이스(interface)를 사용하여 구현됩니다.

1. List 인터페이스
2. Set 인터페이스
3. Map 인터페이스

이 중에서 List와 Set 인터페이스는 모두 Collection 인터페이스를 상속받지만, 구조상의 차이로 인해 Map 인터페이스는 별도로 정의됩니다. 따라서 List 인터페이스와 Set 인터페이스의 공통된 부분을 Collection 인터페이스에서 정의하고 있습니다.

<br>

### List vs Set vs Map 차이점 설명
1. List
   1. 순서가 있는 데이터의 집합으로, 데이터의 중복을 허용함.
   2. Vector, ArrayList, LinkedList, Stack, Queue
2. Set
   1. 순서가 없는 데이터의 집합으로, 데이터의 중복을 허용하지 않음.
   2. HashSet, TreeSet
3. Map
   1. 키와 값의 한 쌍으로 이루어지는 데이터의 집합으로, 순서가 없음.
   2. 이때 키는 중복을 허용하지 않지만, 값은 중복될 수 있음.
   3. HashMap, TreeMap, Hashtable, Properties

<br>

### Vector와 List 차이에 대해 설명하시오.

- Vector
  - 일반적인 배열처럼 개체들을 연속적인 메모리 공간에 저장한다. 즉, iterator 뿐 아니라 position index(operator [])로도 접근이 가능하다는 것이다.
  - 동적으로 확장/축소가 가능한 동적 배열(dynamic array)로 구현되어 있다.
- List
  - double linked list로 구현되어 있다.
  - 노드가 양 쪽으로 모두 연결 되어 있으며 삽입/삭제가 자주 발생하는 경우에 용이하다.

|   | 장점  | 단점 |
| :--------: |:---------------| :----- |
| Vector      | - 개별 원소들 접근 가능<br>- 원소를 마지막에 삽입 하는 것이 빠름<br>- 랜덤으로 원소 순회가 가능<br>- 개별 원소에 대한 접근 속도가 빠름 | - 컨테이너 끝이 아닌 곳에 삽입/제거시 성능이 현전히 떨어짐<br>- 동적이라 확장/축소가 편하나 확장시 비용이 크다. |
| List | - 컨테이너 어느 위치에서라도 삽입/제거가 빠름<br>- 원소들의 컨테이너 내 이동이 빠름 | - 원소의 인덱스로 직접 접근이 불가능함<br>- 특정 원소에 접근하려면 처음이나 끝부터 선형 탐색을 해야함<br>- 컨테이너 내 순회가 forward / reverse만 가능하여 느림<br>- 원소간 상호 연결 정보를 위해 추가적 메모리 비용 발생 |

> 자료출저 : https://jaehogame.tistory.com/entry/STL-Vector%EC%99%80-List-%EC%B0%A8%EC%9D%B4%EC%A0%90

<br>

### HashMap vs HashTable vs ConcurrentHashMap의 차이를 설명하시오.
위에 나열된 클래스들은 [Map 인터페이스](https://jdm.kr/blog/197)를 구현한 콜렉션들입니다. 기본적으로 Map 인터페이스를 구축한다면 <key, value>구조를 가지게 됩니다. 하나씩 살펴봅시다. (위 링크에 코드도 참고하면 좋다.)
- Hashtable
  - put, get과 같은 주요 메소드에 synchronized 키워드가 선언 되어 있습니다. 또한 key, value에 null을 허용하지 않습니다.
- HashMap
  - 주요 메소드에 synchronized 키워드가 없습니다. 또한 Hashtable과 다르게 key, value에 null을 입력할 수 있습니다.
- ConcurrentHashMap
  - HashMap을 thread-safe 하도록 만든 클래스가 ConcurrentHashMap입니다. 하지만 HashMap과는 다르게 key, value에 null을 허용하지 않습니다. 또한 putIfAbsent라는 메소드를 가지고 있습니다.

> 해시 충돌 : https://d2.naver.com/helloworld/831311
퍼포먼스 차이 : https://www.nagarro.com/en/blog/post/24/performance-improvement-for-hashmap-in-java-8
