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
