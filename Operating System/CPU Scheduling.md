## CPU Scheduling

### CPU burst와 I/O burst

프로그램은 CPU burst와 I/O burst의 연속이다. 이는 프로그램마다 빈도 및 길이가 다르기 때문에(여러 종류 job(process) 섞여 있음) CPU 스케줄링이 필요하다

CPU를 짧게 쓰고 I/O가 끼어드는 형태 → I/O bound job

CPU를 길게 쓰고 I/O 끼어드는게 적은 형태 → CPU bound job

⇒ interactive job에게 적절한 response 제공, CPU와 I/O 장치 등 시스템 자원 효율적 사용

### 프로세스 특성

- I/O-bound process: CPU를 잡고 계산하는 것보다 I/O에 더 많은 시간 필요
    
    이는 사람과 interaction이 많은 job을 의미
    
    many short CPU bursts
    
- CPU-bound process: 계산 위주의 job
    
    few very long CPU bursts
    

### CPU Scheduler & Dispatcher

**CPU Scheduler**

Ready 상태의 프로세스 중 CPU를 줄 프로세서를 고른다

운영체제 안에 코드 형태

**Dispatcher**

CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스를 넘기는 코드

context switch(문맥교환)라고 함

스케줄링 필요한 경우

- nonpreemptive(비선점형, 자발적 반납)
    - running → blocked: I/O 요청하는 시스템 콜 경우
    - Terminate
- preemptive(선점형, 강제적 반납)
    - running → ready: 할당 시간 만료로 timer interrupt
    - blocked → ready: I/O 완료 후 interrupt

### Scheduling Criteria/Performance Index/Performance Measure(성능 척도)

시스템 입장

- CPU utilization(이용률): 바쁘게 CPU를 많이 사용되었는가
- Throughput(처리량/산출량): 시간안에 몇개의 작업을 처리 했는가

프로그램 입장

- Turnaround time(소요시간, 반환시간): CPU를 쓰러 들어와서 쓰고 난 다음 나갈때까지 시간
- Waiting time(대기 시간): CPU를 기다리는 시간
- Response time(응답 시간): CPU 쓰러 들어와서 처음으로 CPU를 가지게 되기까지의 시간

### Scheduling algorithm

### -FCFS(First-Come First-Served): 먼저 온 순서대로 처리

- Convoy effect: 긴 프로세스 뒤에 짧은 프로세스가 있을 경우 대기 시간에 안좋은 효과가 나타남

### -SJF(Shortest-Job-First): 
CPU burst time이 짧은 프로세스에게 먼저 스케줄

minimum average waiting time을 보장

- nonpreemptive - 한번 잡으면 CPU burst가 완료될 때까지 선점
- preemptive - Shortest Remaining Time First: 수행중이더라도 더 짧은 프로세스 도착 시 뺏어감
- 문제점
    - Starvation(기아 현상): 긴 프로세스는 계속해서 기다리게 될 수 있다
    - CPU 실행시간을 미리 알 수 없다
    추정만 가능(과거 CPU burst time 이용해서 추정-exponential averaging)

### -Priority Scheduling: 
우선순위가 높은 프로세스에게 CPU 할당

- Preemptive, nonpreemptive 방법이 있음
- Problem: starvation ⇒ Solution: Aging(오래 기다릴 시 우선순위를 높여줌)

### -Round Robin(RR): 
각 프로세스는 동일한 크기의 할당시간을 가지고, 끝나면 다음 프로세스 넘겨줌

- 장점: 짧은 응답시간을 가지게 된다
n개의 프로세스가 q time unit으로 할당시간을 가질 때 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다
- 대기시간이 자신의 프로세스 CPU 사용 시간과 비례함
- 성능(q 높 → FCFS, q 낮 → context switch 오버헤드 커짐)
- 일반적으로 SJF보다 average turnaround time이 길지만 response time이 더 짧다

### -Multilevel Queue

- Ready Queue를 여러 개로 분할 → foreground(interactive)/background(batch-no human interaction)
- 각 큐 독립적인 스케줄링 알고리즘 → foreground(RR)/background(FCFS)
- 이때 큐에 대한 스케줄링이 필요한데,
    - Fixed priority scheduling - foreground 다 된 후 background → starvation 가능
    - Time slice - 각 큐에 적당한 CPU time 할당

### -Multilevel Feedback Queue

- 프로세스가 다른 큐로 이동이 가능

### -Multiple-Processor Scheduling

CPU(프로세서)가 여러개인 경우

- Homogeneous processor: queue 한줄로 세워서 프로세서가 알아서 꺼내가도록 한다
- Load sharing: 특정 프로세서에만 프로세스가 몰리지 않도록 부하를 적절히 공유
- symmetric multiprocessing(SMP): 각 프로세서가 각자 알아서 스케줄링
- asymmetric multiprocessing: 하나의 프로세서가 전체적인 관리

### -Real-Time Scheduling

dead-line이 보장된 경우

- Hard real-time system → Hard real-time task는 정해진 시간 안에 끝내도록 스케줄링
- Soft real-time computing → soft-real time task는 일반 프로세스에 비해 높은 priority

### -Thread Scheduling

- Local Scheduling - User level thread(사용자 수준)
- Global Scheduling - Kernel level thread(운영체제 단위)

### Algorithm Evaluation

- Queueing models
    - CPU에 도착율(arrival rate)과 처리율(service rate)을 확률분포로 주어질 때 계산
- Implementation(구현) & Measurement(성능 측정)
    - 실제 시스템에 알고리즘 구현해 실제 작업에 대해 성능 측정 비교
- Simulation(모의 실험)
    - 알고리즘 모의 프로그램 작성해 trace를 입력으로 하여 결과 비교
