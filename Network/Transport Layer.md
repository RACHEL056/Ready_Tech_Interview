## Transport Layer

### Multiplexing / Demultiplexing

Multiplexing

Application Layer에서 내려오는 메세지를 segment형태로 만들어주는 작업

Demultiplexing

Segment 안에 있는 메세지를 Application Layer의 어느 소켓으로 전달하는 지

이는 Segment의 Header 정보를 통해 어느 프로세스의 소켓으로 전달할지 알 수 있다.

Header → source port # + dest port # + other header fields

UDP demux - connectionless demux

destination IP / destination port 로 소켓 결정

TCP demux - connection oriented demux

source IP / source port / destination IP / destination port 로 소켓 결정

### UDP

- 기능
    1. Multiplexing / Demultiplexing
    2. 오류 검출 → checksum으로 오류 발생 시 drop
- Segment Header의 구성 (4 field)
    
        source port # + dest port # + length + checksum 각 16 bit
