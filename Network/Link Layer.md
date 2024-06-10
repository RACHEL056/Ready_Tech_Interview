## Link Layer

GWR로 packet을 보낼 때, 여러 매체에서 같이 보내지기 때문에 collision이 발생할 수 있다 !
충돌 발생 시 신호가 섞이게 되어 GWR는 이해하지 못하므로 이를 Link Layer에서 해결한다

Transport, Network → operating system안에 코드로 구현되어 있음

Link → Network interface card에 있음

### Access Protocol

전용선으로 이루어지지 않은 네트워크 안에서 매체들은 “broadcast medium”들

⇒ medium access control(매체가 접근할 때 제어) “**MAC**” protocol

이상적인 상황

링크의 bandwidth가 R bps일때,

1. 하나의 노드가 전송할 때에는 R 속도로 데이터 전송
2. M개의 노드가 전송할 때에는 R/M 속도로 전송
3. 위 두가지 기능이 가능하되 분산처리
4. 간단

### MAC protocols

1. channel partitioning
2. random access
3. taking turns

### Channel partitioning

**TDMA(time division multiple access)**

사용자마다 각각의 slot을 가짐 → 자원의 낭비가 심함

**FDMA(fequency division multiple access)**

각자 데이터를 보내는 주파수를 분할 → TDMA와 같이 사용 효율 측면에서 낭비

⇒ 사용자가 많을수록 유리하다

### Random access

내가 보내고자 할 때 데이터를 보낸다!
ex) (slotted) ALOHA, CSMA, CSMA/CD, CSMA/CA

**CSMA(carrier sense multiple access)**

전송하기 전에 듣는다! 누군가가 이야기 할때에는 wait하고 이후에 transmit
하지만 여전히 충돌이 일어날 수 있음 → 기다린 후 동시에 이야기를 하려고 할 때
⇒ propagation delay로 인해 다른 매체 전송을 모르는 상황

**CSMA/CD(CSMA collision detection)**

CSMA에서 충돌을 감지했을 때 즉시 멈추는 방법
충돌 이후에 “**binary(exponential) backoff**” ⇒ m번의 충돌이 있을 때 {0,1,2…2^m-1} 중 랜덤으로 선택해 기다림

⇒ 사용자가 적을수록 유리하다

### Taking Turn

**Polling**

master controller가 존재해서 순서를 정해줌(master node - slave node)

→ master에 의존하므로 master node가 망가지면 전체가 마비된다

**Token passing**

token을 가진 사용자만이 데이터를 보낼 수 있다.
전송이 끝낸 후 토큰을 다른 매체에게 넘기면서 전송
→ 토큰을 잊어버릴 경우 문제 생김

### LANs(Local Area Networks)

GWR에 연결된 여러 매채들의 집합들 이때 매체들을 제어하기 위해 MAC(Medium Access Control)

**Ethernet**

- Physical topology
    1. bus(버스형): 모든 노드가 같은 도메인에 위치, 한 줄에 여러 매체가 연결
    2. star(성형): 활성화된 스위치가 중앙에 위치해서 연결되어 매체와 연결
- Frame structure ⇒ HDR+ IP packet
- Ethernet을 사용하는 MAC protocol은 CSMA/CD

유선 Ethernet 상황에서 collision이 발생하면 retransmit을 한다 ⇒ 충돌이 발생 안하면 GWR에 도달

**??** collision이 발생했는데 MAC layer에서 충돌을 감지하지 못한다면? (MAC layer ACKS를 사용X)
→ A——>E로 frame을 보내다가 propagation delay로 충돌 감지X

⇒ frame을 다 보낸 후에도 말을 하는 상태 유지하기 위해 minimum frame size = 64byte

**MAC addresses and ARP(Address Resolution Protocol)**

- 48bit MAC address (24bit 제조회사 + 24bit 인터페이스 고유 #)
hostname 및 IP address는 바뀔 수 있지만, MAC 주소는 존재 자체로 변경 X
- Frame에 GWR’s Mac address는 모름 ⇒ IP주소를 통해 이를 알아내야함
각각의 노드에 ARP table(IP address / MAC address /TTL 매핑 테이블) 존재

ARP request(IP address 포함)로 broadcast를 하여 GWR MAC address 알게됨

**Switches**

link layer device로 연결만 해주는 장치

collision domain을 분리시켜줌

host들에게는 보이지 않음 → host는 ethernet cable만을 알고 있고 이후의 것들은 모르는 상태

-switch forwarding table

이를 통해 스위치에 연결된 매체들 간에 충돌없이 보내줄 수 있음

self-learning을 통해 table 생성 (위치를 모를경우 flood를 통해 위치를 알게됨)

스위치 간에도 연결 가능함

### data center networking

많은 host에게 요청되는 server는 여러 서버를 두어 프로세스를 처리하게 된다
