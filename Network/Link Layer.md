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
