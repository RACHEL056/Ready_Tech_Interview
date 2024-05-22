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
entry section과 exit section을 통해 lock을 걸게 된다

**Algorithm**

충족 조건

1. Mutual Exclusion(상호 배제): 한 프로세스 critical section 수행 시 다른 프로세스 접근X
2. Progress(진행): 아무도 critical section 있지 않은 상태에서는 프로세스 접근 가능하도록
3. Bounded Waiting(유한 대기): 요청이 허용될 때까지 대기하는 시간이 유한. starvationX

Algorithm 1

```c
int turn;
initially turn = 0
```

turn을 통해 critical section에 들어가는 프로세스를 정해준다

critical section을 항상 교대로 들어가게 되어있음 → 프로세스가 critical section에 들어가려는 빈도가 다를경우 문제 발생(progress 조건X)

Algorithm 2

```c
boolean flag[2];
initially flag[모두] = false
```

flag를 통해 critical section에 들어가고 싶다는 상태 의미

아무도 못들어가고 끊임 없이 양보하는 상황이 생길 수 있음(progress 조건X)

Algorithm 3(Peterson’s Algorithm)

1과 2를 섞은 알고리즘

```c
do {
    flag[i] = true;
    turn = j
    while (flag[j] && turn == j);
    critical section
    flas[i] = false;
    remainder section
} while(1);
```

3가지 조건 모두 만족

두가지 프로세스가 같이 들어가려 할 때 turn을 통해 들어가게 해줌

문제점: busy waiting(= spin lock) → 계속 CPU와 memory를 쓰면서 wait 하게 됨

### Synchronization Hardware ⇒ Test & Modify

하드웨어적으로 atomic하게 수행할 수 있도록 지원

Test_and_set(a) → 1. Read: 읽은 후 2:TRUE로 값을 바꿔줌

```c
//boolean lock = false;

//Process Pi
    do {
        while(Test_andSet(lock));
        critical section
        lock = false;
        remainder section
    }
```
