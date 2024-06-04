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

### Problems of Synchronization

1. Bounded-Buffer Problem(Producer-Consumer Problem)
2. Readers and Writers Problem
3. Dining-Philosophers Problem

**Bounded-Buffer Problem**

공유 메모리의 버퍼에 데이터를 생산하는 “Producer”, 데이터를 사용하는 “Consumer”이 존재

shared data → buffer 자체 및 buffer 조작 변수(empty/full buffer 시작 위치)

이때 필요한 semaphore

1. 동시에 공유 buffer에 접근하는 것을 막기 위해 공유 버퍼 전체에 lock 하는 semaphore(mutual exclusion)
2. 버퍼가 다 차있거나 비어있을 때를 알 수 있는 가용 자원을 세는 semaphore (resource count)

*Semaphore

variables: full=0, empty=n, mutex=1

이때 mutex는 lock을 위한 변수

```c
Producer

do{ ...
produce an item in x
...
P(empty); //buffer에 여유공간이 있는지 확인
P(mutex); //여유 공간이 있을 때 mutex 값으로 buffer에 lock 걸기
...
add x to buffer
...
V(mutex); //lock 해제
V(full); //빈 공간이 하나 없어짐
}while(1);

Consumer

do{

P(full) //buffer이 비어있는지 확인
P(mutex); //비어있지 않을 대 자원을 가져오기 위해 lock
...
remove an item from buffer to y
...
V(mutex); //lock 해제
V(empty); //buffer에 빈 공간 하나 생김
...
consume the item in y
...
}while(1);
```

**Readers-Writers Problem**

Writer만 상호 배제가 필요하며 reader는 동시에 읽어도 상관이 없는 상황

shared data→ DB 자체, readcount;(DB에 접근 중인 reader 수)

*Semaphore

variables: mutex=1, db=1

이때 db는 DB 자체에 lock을 걸기 위한 S, mutex는 reader수를 증가시킬 때 lock 걸기 위한 S

```c
Writer

P(db);
...
writing DB is performed
...
V(db);

Reader

P(mutex);
readcount++;
if(readcount==1) P(db); //처음으로 읽기 시작하면 writer 접근하지 못하게 db 잠금
V(mutex);
...
reading DB is performed
...
P(mutex);
readcount--;
if(readcount==0) V(db); //마지막으로 읽고 이후에 읽는 사용자가 없으면 writer 접근 허용
V(mutex)
```

이때 writer가 지나치게 기다릴 경우 starvation이 발생할 수 있음

**Dining-Philosophers Problem**

생각하거나 먹는 두가지 행위를 하는 철학자가 원탁에 있음. 이때 먹을때는 왼쪽과 오른쪽의 젓가락을 잡아서 먹어야 함

variable → chopstick[5] (이때 젓가락은 1로 초기화)

```c
Philosopher i

do{
P(chopstick[i]);
P(chopstick[(i+1)%5]);
...
eat();
...
V(chopstick[i]);
V(chopstick[(i+1)%5]);
...
think();
...
}while(1);
```

이때 문제점

→ deadlock 가능성: 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 집어 아무도 오른쪽 젓가락을 가지지 못해 하염없이 기다리는 경우

해결 방안

1. 4명의 철학자만이 테이블에 동시에 앉을 수 있도록(배고픈 사람만 식탁에 앉기)
2. 젓가락을 두 개 모두 집을 수 있을 때만 젓가락 집기
3. 비대칭: 착수 철학자는 왼쪽 젓가락부터, 홀수 철학자는 오른쪽 젓가락부터 집기

*Semaphore(2번 경우)

```c
enum{thinking, hungry, eating} state[5]; // 철학자의 상태
semaphore self[5]=0; //철학자가 왼쪽, 오른쪽 젓가락을 잡을 수 있는 권한
semaphore mutex=1; //state는 공유 자원으로 이를 lock 하는 S

Philosopher i
do{ 
pickup(i);
eat();
putdown(i);
think();
}while(1);

void pickup(int i){
P(mutex);
state[i]=hungry;
test(i);
V(mutex);
P(self[i]); //test에서 if 조건이 만족이 되지 않으면 wait
}

//좌우 철학자가 먹는 상태가 아니고 해당 철학자가 배고픈 상태일 때 먹는 권한을 줌
void test(int i){
    if (state[(i+4)%5]!= eating && state[i]==hungry && state[(i+1)%5]!=eating) {
    state[i]=eating;
    V(self[i]); //이때 권한은 0으로 초기화 되어 있어 V semaphore로 1을 주어 권한을 줌
    }
}

void putdown(int i) {
P(mutex);
state[i]=thinking;
test((i+4)%5); //좌우 철학자가 배고픈 상태였는데 못먹어서 기다리는지를 검사
test((i+1)%5);
V(mutex);
}
```

### Monitor

Semaphore의 문제점

코딩이 힘들다

정확성 입증이 어렵다

자발적 협력이 필요하다

한번의 실수로 모든 시스템에 치명적 영향을 끼친다

⇒ high-level synchronization construct인 “Monitor” 사용

모니터 안에 공유 데이터에 대한 변수를 정의하고 procedure을 정의해둠.

이때 procedure에 동시에 활동 불가능하도록 설정이 되어있어 Lock을 하지 않아도 된다

```c
monitor monitor-name
{
    shared variable declarations
    procedure body P1(...){
    ...
    }
        procedure body P2(...){
    ...
    }
    procedure body Pn(...){
    ...
    }
    {
        initialization code
    }
}
    
```

**condition variable**

process를 잠들게 하거나 줄세우기를 하기 위한 변수(wait, signal 연산에 의해서만 접근 가능)
