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

### Semaphores

“추상화” → 논리적으로 정의하는 것이지 실제로 구현되는 것과 별개의 자료형

Semaphore **S** 

integer variable
가정: 두 가지 atomic 연산에 의해서만 접근 가능

```c
//자원을 가져가는 연산, Lock을 거는 과정
P(S):
while(S<=0) do no-op; //wait 자원이 가져가서 없는 상태이기 때문
S--; //자원이 양수가 되는 경우 자원을 가져가서 사용하는 것
//이때 양수가 될 때까지 기다려야 하므로 => busy wait 문제 발생

//자원을 다 사용한 후 내놓는 연산, Lock을 푸는 과정
V(S):
S++ //자원 반납
```

**Critical Section of n Processes**

busy-wait를 해결하기 위해 critical section을 활용

```c
Synchronization variable
semaphore mutex; //초기에 1: 1개가 CS에 들어갈 수 있다

Process Pi;
do{
    P(mutex);
    critical section
    V(mutex);
    remainder section
} while(1);
```

**⇒ Block/Wakeup Implementation**

```c
//Semaphore 정의
typedef struct
{ int value;
    struct process *L; //wait queue
} Semaphore
```

block → block을 호출한 프로세스를 suspend시키고, 이 프로세스의 PCB를 semaphore 대한 wait queue에 넣어둠

wakeup(P) → block된 프로세스 P를 wakeup, 프로세스의 PCB를 ready queue로 옮김

```c
//자원의 여분이 있을 경우 획득, 아닐경우 block
//S.value는 자원의 개수를 세는 의미기 보다 자원을 기다리는 프로세스가 있는 상황인지 여분이 있는 상황인지 사용
P(S):

S.value--;
if(S.vlaue<0) //음수일 경우 자원 여분 없는 경우
{
    add this process to S.L;
    block();
}
```

```c
V(S):

S.vlaue++;
if(S.value<=0){ //자원을 내놓았는데도 0 이하인 경우는 다른 프로세스가 큐에 존재한다는 것
    remove a process P from S.L;
    wakeup(P);
}
```

**Busy-wait vs Block/wakeup**

critical section 길이가 긴 경우  → Block/Wakeup

critical section 길이가 매우 짧은 경우 → Block/Wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음(active 상태를 wait 상태로 바꿔야 하기 때문)

**Type of Semaphore**

- Counting semaphore
    
    도메인이 0 이상인 임의의 정수 값, resource counting에 사용
    
- Binary semaphore(=mutex)
    
    0,1값만 가질 수 있는 경우, mutual exclusion(lock/unlock)에 사용
    

**유의할 점**

- Deadlock: 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event 무한히 기다리는 현상
    
    → 다른 프로세스가 하나씩 semaphore을 차지하고 상대의 것을 요구하는 상황
    
- starvation: indefinite blocking. 프로세스가 suspend 된 후 세마포어 큐에서 빠져나갈 수 없는 현상
