## Process

### Process is a program in execution

프로세스는 실행중인 프로그램을 의미한다

- 프로세스 문맥(context) - 프로세스의 현재 상태를 나타내는 것
    
    CPU 수행 상태를 나타내는 것 - 현재 어디까지 실행이 되었는가? 
    
    - Program Counter
    - 각종 register
    
    프로세스의 주소 공간(메모리 관련) - code, data, stack
    
    프로세스 관련 커널 자료 구조(프로세스를 관리하는 운영체제 관련) - PCB, Kernel stack
    
    ⇒ 현재 여러 프로세스가 실행되기 때문에 프로세스 문맥을 알아야 한다
    
- 프로세스 상태
    
    프로세스는 상태(state)가 변경되며 수행된다 - CPU하나인 상태
    
    **이는 운영체제의 상태가 아니다
    
    - Running - CPU를 잡고 instruction 수행중
    - Ready - CPU를 기다리는 상태(다른 조건 모두 만족)
    - Blocked(wait, sleep) - CPU를 주어도 당장 instruction 수행 불가능한 상태
        
        I/O등의 event를 (스스로) 기다리는 상태
        
        자신이 요청한 event 만족되면 ready
        
    - Suspended(stopped) - 외부적인 이유로 프로세스 수행 정지 상태
        
        중기 스케줄러 관련
        
        외부에서 resume해 주어야 active
        
    - New - 프로세스 생성중인 상태
    - Terminated: 수행(execution) 끝난 상태

- PCB - Process Control Block
    
    운영체제가 각 프로세스 관리하기 위해 프로세스 당 유지하는 정보
    
    구성요소
    
    1. OS가 관리상 사용하는 정보 - Process state, Process ID, scheduling information..
    2. CPU 수행 관련 하드웨어 값 - Program counter, registers
    3. 메모리 관련 - code, data, stack 위치 정보
    4. 파일 관련 - open file descriptors…
    
- 문맥 교환(context switch)
    
    CPU 한 프로세스에서 다른 프로세스로 넘겨주는 과정
    
    이때 이전 작업 상태를 저장해 이후 다시 그 문맥부터 실행됨
    
    ** Interrupt 나 system call 발생 시 context switch 가 일어나지 않음
    
    ** system call의 커널 모드로 넘어가는 상황에서도 문맥 일부를 저장하지만 문맥교환X
    
    ** 다른 프로세스로 넘어갈 때 발생함 - timer interrupt, I/O 요청 system call
    

- 스케줄링 큐
    - Job queues - 현재 시스템 모든 프로세스의 집합
    - Ready queues - 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스 집합
    - Device queues - I/O device 처리를 기다리는 프로세스 집합
    
    ⇒ 프로세스들은 큐들을 오가며 수행된다
    
- 스케줄러(Scheduler) - 순서를 정해주는 것
    - Long-term scheduler(장기 스케줄러, job scheduler)
        
        프로세스가 생성되고 ready queue 즉 메모리에 올라가는 것 결정(시작 시)
        
        memory를 주는 문제 - memory에 올라가 있는 프로그램 수 조정(degree of multiprogramming)
        
    - Short-term sheduler(단기 스케줄러, CPU scheduler)
        
        어떤 프로세스를 다음에 running 할지 결정
        
        CPU를 주는 문제 - 이는 빨라야 함
        
    - Medium-term scheduler(중기 스케줄러, Swapper)
        
        여유 공간 마련을 위해 프로세스를 통째로 메모리 → 디스크로 보냄(중간)
        
        memory를 주는 문제 - memory에 올라가 있는 프로그램 수 조정(degree of multiprogramming)
