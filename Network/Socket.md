OS에서 제공하는 서비스를 사용하기 위해서 인터페이스를 사용해야 한다

프로세스와 프로세스 간의 통신을 위한 API = Socket

## Socket

소켓 종류

1. TCP Socket
2. UDP Socket

### Socket API

전체적인 흐름

TCP Server

socket() → bind() → listen() → accept() 까지 진행 후 이후에 client 요청이 있을 때까지 block 해둠

**bind() 에서는 포트를 연결

TCP Client socket() → connect() 로 TCP 서버와 견고한 연결이 이루어짐

이후 client와 server은 read() and write()의 반복

### Function - TCP

Server에서의 소켓 생성

- 소켓의 생성(type에서 TCP인지 UDP인지 결정)
    
    ```c
    int socket (int domain, int type, int protocol);
    ```
    
- 소켓에 IP주소와 포트 넘버를 묶기
    
    ```c
    int bind (int sockfd, struct sockaddr*myaddr, int addrlen);
    ```
    
- socket을 passive state로 두기 → 어느 정도의 request를  큐에 담아서 처리할 것인지
    
    ```c
    int listen (int sockfd, int backlog)
    //backlog가 처리하는 양 의미
    ```
    
- 클라이언트에게 연결을 기다리겠다 - block 해둠
    
    ```c
    int accept (int sockfd, struct sockaddr*cliaddr, int* addrlen);
    ```
    

Client에서는 socket 생성 후 connect

```c
int connect (int sockfd, struct sockaddr*servaddr, int addrlen);
```

Clint와 Server간 

- Stream에 데이터 쓰기(Write)
    
    ```c
    int write (int sockfd, char* buf, size_t nbytes);
    ```
    
- Stream에서 데이터 읽기(Read)
    
    ```c
    int read (int sockfd, char* buf, size_t nbytes);
    ```
    

데이터 교환이 다 끝난 후

```c
int close (int sockfd);
```

따라서 다른 프로세스가 사용 가능하게 한다.
