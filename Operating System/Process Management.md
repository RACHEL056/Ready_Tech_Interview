## Process Management

### Process Creation 프로세스 생성

Copy-on-write(COW) → write가 발생하면 copy하겠다

부모 프로세서(Parent)가 자식 프로세스(children)을 생성

- 트리(계층 구조) 형성
- 자원을 필요로 함 → 운영체제로부터 받는다
- 자원의 공유 → 부모와 자식이 공유할 수도 있고, 공유하지 않을 수도 있음
- 수행(execution) → 공존하며 수행 or 종료 후 수행
- 주소공간(address space)
    
    자식은 부모의 공간 복사(binary and OS data), 이후 공간에 새로운 프로그램 올림
    
- 유닉스 예, fork() 시스템 콜로 새로운 프로세스 생성 → exec() 시스템 콜을 통해 새로운 프로그램 올림

### Process Termination

마지막 명령을 수행한 후 운영체제에게 이를 알려줌(**exit**) → 자발적

부모 프로세스가 자식의 수행 종료(**abort**) → 외부에서 종료

- 자식이 할당 자원 한계치 넘을 경우, 자식에게 할당된 태스크가 더이상 필요하지 않는 경우, 부모가 종료(exit)하는 경우

### fork() 시스템 콜: create a child

```c
int main()
{ int pid;
    pid = fork();
    if(pid == 0)  //0은 child
        printf("\n Hello, I am child!\n");
    else if (pid > 0) //양수는 parent
        printf("\n Hello, I am parent!\n")
}
```

위와 같은 코드가 fork()를 통해 똑같은 프로세스가 복사 되면서 child 가 생성된다

### exec() 시스템 콜: overlay new image

```c
int main()
{ int pid;
    pid = fork();
    if(pid == 0)  //0은 child
        printf("\n Hello, I am child! Now I'll run date \n");
        execlp("/bin/date", "/bin/date", (char *)0);
    else if (pid > 0) //양수는 parent
        printf("\n Hello, I am parent!\n")
}
```

execlp 할 경우 완전히 새로운 프로세스가 덮어씌워지고, 이는 되돌릴 수 없다

```c
//exec() 형태
execlp("프로그램 이름", "프로그램 이름", ... , (char *)0);
```

### wait() 시스템 콜: sleep until child is done

자식 프로세스가 종료될 때 까지 기다리는 형태(block 상태) → 종료 후 깨운다(ready 상태)

### exit() 시스템 콜: frees all the resources, notify parent

프로세스의 종료

- 자발적 종료 → 마지막 statement 수행 후 exit() 시스템 콜(명시X)
- 비자발적 종료
    - 부모 프로세스가 자식 프로세스 강제 종료(자식 프로세스 한계치 넘거나 할당된 태스크X)
    - 키보드로 kill, break
    - 부모가 종료하는 경우

### 프로세스 간 협력

- 독립적 프로세스(independent process) → 다른 프로세스의 수행에 영향X
- 협력 프로세스(cooperating process) → 다른 프로세스의 수행에 영향O
- 프로세스 간 협력 메커니즘(IPC: Interprocess Communication)
    - message passing: **커널**로 메세지 전달
        - Direct Communication: 통신하려는 프로세스 명시적 표시
        - Indirect Communication: mailbox(or port) 통해 간접 전달
    - shared memory: 일부 주소 공간 공유
    - thread → 하나의 프로세스에 해당으로 협력으로 보기 어렵지만 동일 process 구성하는 thread간 주소공간 공유
