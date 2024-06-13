## Memory Management

### Logical vs Physical

logical address(virtual address) 논리적 주소

프로세스마다 독립적으로 가지는 주소

각 프로세스마다 0번지부터 시작

CPU가 바라보는 주소는 logical address이다

physical address 물리적 주소

메모리에 실제 올라가는 위치

어떤 프로그램이 어떤 메모리에 올라갈지 결정 = 주소 바인딩(주소를 결정하는 것)

Symbolic Address → Logical Address → Physical address

### 주소 바인딩

1. Compile time binding → 컴파일 시점에 이미 physical address 만들어짐
절대코드(absolute code) 생성 = 새로운 주소 할당하고 싶을 때 다시 컴파일
2. Load time binding → 실행 시 물리적인 메모리에 비어있는 공간에 적재
컴파일러가 재배치가능코드(relocatable code) 생성한 경우 가능

위 두가지는 프로그램이 실행될 때 절대적인 주소 값을 가지게 되고 변경 X
3. Execution time binding(run time binding) → 실행되는 도중에 주소가 이동할 수 있음
CPU가 주소 참조할 때마다 binding 점검, 하드웨어적인 지원 필요(주소 변환 용)

**Memory-Management Unit(MMU)**

logical address를 physical address로 매핑해 주는 하드웨어 장비

동작: 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register(=relocation register)값 더해주기
이때 user program은 logical address만을 다루며 실제 physical address를 알지 못한다

구성: relocation register(접근할 수 있는 물리적 메모리 주소의 최소값) + limit register(가상 메모리 크기,  논리적 주소의 범위)

**Terminologies/ 용어**

**-Dynamic Loading** 

프로세스 전체를 미리 다 올리는 것이 아닌 해당 루틴이 불려질 때 메모리에 load 하는 것
→ memory utilization 향상

운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능 → 프로그래머가 라이브러리를 통해 쉽게 구현

**-Overlays**

메모리에 프로세스의 부분 중 실제 필요한 정보만 올림

이는 초창기 컴퓨터 시스템에서 프로그래머가 수작업으로 진행 “Manual Overlay”

프로세스의 크기가 메모리보다 클 때 유용

**-Swapping**

프로세스를 일시적으로 메모리에서 backing store(=swap area)로 쫓아내는 것

swap out / swap in

swap time은 대부분 transfer time(swap되는 양에 비례하는 시간)임

**-Dynamic Linking**

Linking을 실행시간(execution time)까지 미루는 기법
이는 라이브러리가 실행 시 연결도미, 라이브러리 호출 부분에 위치 포인터를 저장(stub: 작은 코드)

### Allocation of Physical Memory

| 구조 | 영역 |
| --- | --- |
| OS 상주 영역 | 낮은 주소 영역 |
| 사용자
프로세스
영역 | 높은 주소 영역 |

할당 방법 

→ Contiguous allocation(연속적인 공간 적재) & Noncontiguous allocation(여러 영역 분산 적재)

**Contiguous Allocation**

1. 고정 분할 방식(Fixed partition)
    
    물리적 메모리를 몇 개의 영구적 분할로 나눔 → Internal/external fragmentation 발생
    
2. 가변 분할 방식(Variable partition)
    
    프로그램 크기를 고려해 할당 → external fragmentation만 발생
    
    **Hole - 가용 메모리 공간**
    
    운영체제는 할당공간, 가용공간(hole) 정보를 유지해 프로세스가 도착하면 수용 가능한 hole을 할당
    
    **Dynamic Storage-Allocation Problem**
    
    가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole 찾기
    
    1. Fist-fit: 최초로 찾아지는 hole
    2. Best-fit: 만족하는 hole 중 가장 작은 hole
    3. Worst-fit: 가장 큰 hole
    
    → first-fit과 best-fit이 속도와 공간 이용률 측면에서 효과적
    
    Hole을 한군데로 밀어넣는 방법 → compaction
    but, 비용이 많이 들고 항상 가능하지 않음
