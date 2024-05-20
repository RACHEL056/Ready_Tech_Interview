## Process Synchronization

### 데이터 접근

1. Data 담긴 S-box(Storage)
2. 연산할 data가 S-box에서 E-box(Storage → Execution)
3. E-box에서 연산
4. 연산 결과(Execution → Storage)

예)

| E-box | S-box |
| --- | --- |
| CPU | Memory |
| 컴퓨터 내부 | 디스크 |

이때 S-box를 공유하는 E-box가 여럿 있는 경우 Race Condition 가능성이 있음

### Race Condition

1. Kernel 수행 중 인터럽트 발생 시
    
    → 수행 중에 인터럽트를 받아들이지 않음(enable/disable interrupt)
    
2. Process가 system call을 하여 kernel mode로 수행 중인데 context switch 일어나는 경우
    
    → 커널 모드 시 할당 시간이 끝나도 CPU를 preempt 하지 않음
    
3. multiprocessor에서 shared memory 내의 kernel data
    
    → 한번에 하나의 CPU만이 커널에 들어갈 수 있도록(커널 전체를 하나의 큰 lock)
    
    → 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock/unlock 하는 방법
    

**문제점** = 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터 불일치(inconsistency) 문제를 발생시킬 수 있음

### The critical-section problem

critical section = 각 프로세스에서 공유 데이터를 접근하는 코드
