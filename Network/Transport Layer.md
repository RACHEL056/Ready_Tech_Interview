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


Transport Layer 밑 계층 Network은 reliable 하지 않음 - Unreliable Channel

    1. Message error 2. Message loss 가 일어남

### How? RDT(Reliable Data Transport)

한번에 한 패킷만 보내고 확인을 한다 !

1. RDT1.0: 완벽한 채널을 이용해서 데이터를 전송할 때
    
    → 따로 기능이 필요 없음
    
2. RDT2.0: 패킷 에러가 발생할 수 있는 채널
    
    →  에러 검출(checksum bits)
    
    피드백(ACKs/NAKs로 수신된 패킷 상태 확인)
    
    재전송(retransmission)
    
    피드백에 에러가 있는 경우가 있어 완벽하지 않음 ! ⇒ 피드백에도 checksum 필요
    
    중복된 패킷인지 다른 패킷인지 구별하기 어려움 ⇒ 패킷에 번호를 붙임(sequence #)
    
    ** Sequence #는 2개(0,1)로 1 bit면 충분하다
    
    - RDT2.2: NAK 없이 ACK만 사용 - ACK에 Seq #를 붙여서 보낸다
    
3. RDT3.0: 패킷 에러와 손실이 있는 채널
    
    → 타이머(Timer) 
    
    타이머를 짧게 잡을 경우 - loss 회복이 빠르지만 네트워크 오버헤드 가능
    
    타이머를 길게 잡을 경우 - 네트워크의 오버헤드는 안 일어나지만 loss 회복이 느림
