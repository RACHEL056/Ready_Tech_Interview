## DeadLock 교착상태

가지고 있는 자원을 내놓지 않고 자원을 요구하는 상태
일련의 프로세스들이 서로가 가진 자원(하드웨어/소프트웨어)을 기다리며 block된 상태

*프로세스가 자원을 사용하는 절차

request → allocate → use → release

### 발생 조건

1. mutual exclusion(상호 배제)
2. No preemption(비선점)
3. Hold and wait(보유대기)
4. Circular wait(순환대기)

- Resource-Allocation Graph 자원 할당 그래프
    
    이를 통해 자원과 프로세스간의 할당된 상태를 알 수 있다
    이때 circle이 만들어지면 
    
    자원당 instance가 하나면 → deadlock
    
    아닐 경우엔 deadlock일 가능성이 있음
    

### 처리 방법

예방(강한 처리)

1. Deadlock Prevention
2. Deadlock Avoidance

발생 후 처리

1. Deadlock Detection and recovery
2. Deadlock Ignorance

**Deadlock Prevention**

발생하는 조건 중 하나를 원천적으로 차단하는 방법

- mutual exclusion
    
    공유해서는 안되는 자원 경우 반드시 성립
    
- hold and wait → 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않는다
    
    방법1. 프로세스 시작 시 모든 필요한 자원 할당
    
    방법2. 자원이 필요할 경우 보유 자원 모두 놓고 다시 요청
    
- no preemption
    
    뺏어올 수 있도록, 선점할 수 있도록 만든다
    
    CPU, memory같이 상태를 저장하고 restore 할 수 있는 자원등에 가능
    
- circular wait
    
    모든 자원 유형에 할당 순서 정한다
    

⇒ Utilization 저하, throughput 감소, starvation 문제

**Deadlock Avoidance**

프로세스가 시작 시 프로세스가 필요로 하는 최대 사용량을 이미 알아 선언하도록 하는 방법

- Single Resource(자원에 인스턴스 하나인 경우) → resource allocation graph algorithm
    
    점선을 통해 미래에 요청할 수 있는 자원을 표시해둠
    이에서 deadlock 위험성이 있는 경우 available 해도 주지 않음
    
- Multiple Resource(자원에 인스턴스 여러개인 경우) → banker’s algorithm
    
    maximum 으로 평생의 최대 사용량을 선언
    추가로 요청할 자원이 가용 자원에서 모두 충족하는가를 판단 → 성립할 경우 자원 할당
