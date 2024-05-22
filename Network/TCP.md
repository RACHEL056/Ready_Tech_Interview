## TCP- Connection-Oriented Transport

- point-to-point: socket 한쌍끼리의 통신
- reliable, in-order byte stream: 유실이 없으며 순서대로인 stream
- full duplex data: 모두가 sender가 될 수도 receiver가 될 수 있다, bi-directional
- send & receive buffer: 보낼 패킷의 저장소, 받은 패킷의 저장소
    
    sender window(buffer) - receive buffer 한 쌍
    
- connection-oriented
- pipelined
- flow controlled: 수신자가 받을 수 있을 만큼만 전달
- congestion control
- pipelined

### TCP segment structure

| 계층 |  |
| --- | --- |
| App | “Message” |
| TCP | “Segment” |
| IP | “Packet” |
| Link | “Frame” |

Header + application data 형태로, 32bits 길이를 가진다(각 줄마다)

source port # + dest port #

sequence number

acknowledgment number

head len + not used … + receive window

checksum + urg data pnter

options

### TCP seq. #’s and ACK

- Seq. #’s = 세그먼트의 데이터의 첫번째 byte의 비트 스트림 “numbers”
    
    sender 기준에서 생성
    
- ACKs = 축적된 ACK로, 받을 seq #를 표시
    
    receiver 기준에서 생성
    
    ex) ACK10 경우, 9 까지 잘 받고 seq #가 10인 패킷을 기다리는 중임을 의미
    
    ** go-back-N경우는 10까지를 잘 받았다는 것을 의미하고 11을 기다리는 중임을 의미한다
    

### Timeout - function of RTT(Round Trip Time)

timeout = RTT로 생각해보자 → RTT 값보다 커지면 timeout

하지만 segment 끼리 RTT 값이 다르다

1. 경로가 다를 수 있기 때문에
2. 같은 경로더라도 queueing delay 값이 상이

EstimatedRTT → 보정한 RTT 값

= (1 - a)*EstimatedRTT + a*SampleRTT(실제 측정한 RTT 값)

하지만 이는 너무 고정적인 값으로 DevRTT로 마진을 남긴다

⇒ TimeoutInterval = EstimatedRTT + 4*DevRTT

### TCP reliable data transfer

- Pipelined segments, cumulative acks, 하나의 타이머

![CS-2.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/723c7bb9-e0a4-4b40-b307-256af13a4052/ca99567e-4065-4e91-957d-419538222cb2/CS-2.jpg)
![CS-3.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/723c7bb9-e0a4-4b40-b307-256af13a4052/1771f0f6-bb7d-4451-9bce-0566cf4c71a2/e5b77d46-8956-4774-84d7-b111e48e3040.png)


### TCP flow control

sender 측이 receive 하는 측의 receive buffer의 available space 만큼만 데이터를 보내는 것

⇒ receiver- drive, HDR의 recv buff에 여유 공간을 알려줌

*Corner case

0bit를 보냈을때에는? sender 입장에서 주기적으로 segment를 보내는데 data는 없거나 1byte만을 담아서 보냄

이를 통해 다시 ACk를 보내면서 여유 공간이 생기는 유무를 확인할 수 있음

### connection management

양 측에 send buffer, receive buffer, 상대의 seq#, 자신의 seq#를 알아야 한다

- 3-way handshake
    
    client → server: SYNbit = 1, Seq = x 연결 의사 표현 및 자신의 seq # x 알려줌
    
    server → client: SYNbit = 1, Seq = y, ACKbit = 1, ACKnum = x+1 
    연결 의사 표현 및 서버의 seq# y 알려줌
    
    client → server: ACKbit = 1, ACLnum = y+1
    
    ⇒ client, server 양 쪽 모두 통신이 잘 되었는지를 확인하기 위해 3way
    2way일 경우 server는 연결이 되어있는지를 잘 모름
    
- closing TCP connection
    
    clientSocket.close();
    
    1. client는 TCP FIN = 1을 보냄(close 상태)
    2. server에서도 데이터를 다 보낸 경우 FIN을 보냄(
    3. client는 ACK를 보내고 혹시 모르는 상황을 대비해 timed wait 시간동안 기다린 후 closed

### congestion control

Network 능력치와 receiver의 한계 → 둘 중 상태가 더 안좋은 쪽에 맞춰줘야 한다

네트워크가 안 막히게 하기 위해 조절 →  네트워크의 상태를 유추해서 진행

중간 네트워크에서는 알려주지 X 따라서 양 끝단의 상태를 보고 유추

3가지 main phases

1. slow start → 조금씩 보내보면서 시작, 네트워크의 상태를 모르기 때문에 겸손, 하지만 증가하는 속도는 exponential하게 증가함
    
    MSS(maximum segment size)단위로 window size 조절
    
2. additive increase → threshold를 넘을 경우 linear하게 증가
3. multiplicative decrease → 패킷 loss가 있을 경우 CongWindow size 절반으로 줄임

대략적인 전송 속도 rate = CongWin / RTT (Bytes/sec)

**TCP Tahoe vs TCP Reno**

Tahoe → packet loss 발생 시 당시의 window size 절반으로 threshold 값을 조정 후 slow start 시작 지점에서 다시 시작

** 패킷 유실 감지 

1. timeout → 유실이 큰 상황

2. 3 duplicate ACK → 패킷 하나만 안보내진 사소한 상황

따라서 2번째 상황일 경우

Reno → window size 줄인 후 threshold 값에서 시작

<aside>
💡  초기 threshold는 어떻게 잡을까?

이는 구현하는 사람에 따라 다르다. 이후 보정하는 방법만 있기 때문에 터지고 난 후 계속 조정해 나가게 된다.

</aside>

### TCP Fairness

과연 여러 TCP connection들은 공평하게 대역폭을 나눠 가질까? Yes

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/723c7bb9-e0a4-4b40-b307-256af13a4052/6f05908b-8caa-47a0-92d1-4c560e7c8efc/Untitled.png)

하지만 TCP 연결 관점에서는 fair 하지만 사용자 입장에서는 TCP connection을 많이 연 사람이 더 많은 이득을 가져가게 된다.
