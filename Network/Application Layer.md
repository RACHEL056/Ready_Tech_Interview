### Application - Transport - Network - Link - Physical

## Application Layer

네트워크 프로세스들이 존재하는 계층

하위 계층인 Transport 제공하는 기능을 사용하는 계층

### Client-server architecture

server - 영구적인 IP 주소

client - 동적인 IP 주소

server 프로세스와 client 프로세스 간의 통신

이때 프로세스 간의 인터페이스 → 소캣(socket)

이때 다른 프로세스를 식별하기 위해서 (IP Address, Port) 로 인식

왜 웹 서비스는 80번 포트로 동일할까? → 서버는 항상 on-service로 DNS가 편리하게 찾아가기 쉽도록 

그렇다면 어플리케이션 계층이 전송 계층에서 필요한 서비스는?

1. **data integrity → 이 서비스만 전송 계층에서 제공해줌**
2. timing
3. throughput
4. security

2~4번은 해당 계층에서 구현함

### Web and HTTP

HTTP(Hypertext transfer protocol)

**Hypertext - 링크가 걸려있는 텍스트

request, response를 통해 통신

TCP를 사용해서 request, response 이전에 TCP connection을 생성해주어야 함

요청받은 서비스만을 처리하고 상대의 상태에 대해서는 무관심

TCP connection에 따라

- non-persistent HTTP - 전송 완료 후 연결 해제
- persistent HTTP - TCP 유지한 채 재사용
