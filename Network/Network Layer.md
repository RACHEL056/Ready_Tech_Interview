## Network layer

TCP에서의 segment들이 어떻게 전달이 될까? 배송에 관해서 공부하는 레이어

IP 프로토콜로 라우터의 개입으로 이루어진다

⇒ 라우터는 네트워크 레이어까지 존재한다(physical, link, network layer)

주요기능

1. Routing   2. Forwarding

### IP: Internet Protocol

### IP Adress(IPv4)

34 bit 숫자, 인터페이스를 식별하는 값(호스트, 라우터 등등에 존재…)

IP 주소를 배정하는 방법 ⇒ 계층화(Network portion + Host portion)

Network(prefix, subnet ID, network ID) - 24bit

Host - 8bit

*subnet mask → 네트워크 크기를 알 수 있음

과거

IP 주소에 class를 나눠 두었음

- Class A - prefix /8 ⇒ host 24bit
- Class B - prefix /16 ⇒ host 16bit
- Class C - prefix /24 ⇒ host 8bit

하지만 유연성이 떨어져 Classless Inter-Domain Routing(CIDR) 사용

- 서브넷 부분이 동적인 길이를 가짐
- 주소 형태: a.b.c.d/x, 이때 x는 서브넷 주소 공간의 비트 수

### Longest Prefix Match Forwarding

여러 매칭되는 IP 주소 중 가장 길게 유사한 prefix 가진 것끼리 매칭해주는 것

### Subnet

- 라우터들을 거치지 않고 접근 가능한 호스트 집합
- 같은 prefix를 가진 집합

**라우터는 여러개의 서브넷에 속한 걸쳐진 존재로, 여러 서브넷을 가짐

이후 인터넷이 상용화되면서 IPv4 주소공간이 부족해져 IPv6를 만듬

but 현재까지 IPv4주소를 사용

### ⇒ NAT(Network Address Translation) 방식

외부에서는 접근 불가능한 local address를 사용(local address는 다른 네트워크에서 재사용 가능)

gateway router를 통해 source 주소, port #(기존 프로세스 찾아가는 주소)를 바꿈

이 경우 port #가 host를 찾아가는 용도로 사용되기 때문에 서버를 사용할 수 없음 

문제점

디자인 상 문제점(layering violation) → layer가 무너짐, IP packet 및 data를 고치게 된다

서버 이용 불가능 → port#를 찾아가는 용도로 사용되어 프로세스에 접근할 수 없음

### Dynamic Host Configuration Protocol(DHCP)

“Host Configuration” → 호스트를 식별해준다 !

어디로 이동하던지 간에 네트워크 접속 시 동적으로 호스트를 식별해준다.

따라서 rental 해주는 느낌으로 IP를 할당한 다음 일정 시간 이후 다시 회수한다.

↔ Static IP: 고유/고정 IP 

**DHCP client-server scenario**

client→server: DHCP discover(broadcast-해당 포트넘버를 가지는 DHCP 서버만 응답, 나머지는 drop)

server→client: DHCP offer(broadcast- transaction ID와 포트넘버로 요청한 client만 응답)

client→server: DHCP request(broadcast- offer을 했지만 선택되지 않은 DHCP 서버들에게 간접적으로 알림)

server→client: DHCP ACK

*GWR(GateWay Router) → NS, DHCP server, NATs, firewall과 같은 기능을 한다

### IP header - identifier(ID) & flag & fragment offset

네트워크 링크에는 MTU(Maximum Transfer Size)가 있는데, 이때 MTU 보다 큰 packet이 들어왔을때?

⇒ fragmentation을 진행해 나중에 reassemble 된다

ID를 통해 동일한 packet 확인

fragflag로 뒤에 fragmentation 패킷 유무(1이면 존재 0 이면 X)

offset으로 순서 확인(/8로 필드 수를 줄임)

### ICMP: Internet control message protocol

source한테 어떤 일이 생겼는지를 알려주는 메세지(네트워크에서 생성되는 메세지) 운반 프로토콜

네트워크 진단을 위해 사용됨

### IPv6

주소: 128bit

Tunneling: IPv4 → IPv6 새로운 형태의 패킷(IPv6)을 구 형식(IPv4)로 변형하여 인식 가능하게 함

### Routing Algorithm

Forwarding은 forwarding table lookup을 하는 행위

Forwarding table은 routing에 의해 만들어짐

⇒ 목표: 목적지까지의 최소의 cost 경로를 찾는 것, 목적지까지 최단 경로를 찾는 것

1. 네트워크가 네트워크 구조 그림(graph)을 다 아는 경우
    
    → link state algorithm
    
2. 이웃의 정보만 아는 경우
    
    → distance vector algorithm
    

**Link State Algorithm**

모든 링크가 link state를 broadcast 해야함 → Dijkstra’s algorithm 사용하여 계산

다음과 같은 다익스트라 알고리즘을 각 노드 기준에서 계산하여 forwarding table을 생성

복잡도: O(n^2)

Oscillations possible → 트래픽에 따라 왔다갔다 하는 현상 발생

```c
Initialization:
//시작하는 노드 "u", N'는 최단 경로가 결정된 노드 집합
N' = {u} 
for all nodes v //이웃된 노드면 값 입력, 아니면 무한대 입력
    if v adjacent to u
        then D(v) = c(u,v)
    else D(v) = 무한대

Loop(반복문)
find w not in N' such that D(w) is a minimum
add w in N'
update D(v) for all v adjacent to w and not in N'
    //기존의 경로와 다른 노드를 거쳐서 가는 경우 비교
    D(v) = min(D(v), D(w) + c(w,v))
until all nodes in N'
```

⇒ 현실적으로 전세계적인 broadcast하기에는 라우터의 양이 방대하기 때문에 하나의 네트워크 안(하나의 주체로만 관리 가능한 단위, 하나의 도메인)에서만 이루어진다

<aside>
💡 네트워크 끼리의 routing algorithm은 별도로 존재

</aside>

**Distance Vector Algorithm**

이웃과만의 정보 교환(분산 처리 시스템) → 직관적이지 않음

Bellman-Ford equation(dynamic programming)

**`dx(y) = min{c(x,v) + dv(y)}` ⇒ recursion**

dx(y) → source x에서 y까지의 최소 경로 cost

c(x,v) → 이웃 v의 cost

**dv(y) → v에서 y까지의 최소 경로 cost(v는 y,w,z,…등 노드들까지의 거리를 배열(distance vector)로 가지고 있어 x에게 전달해줌)
⇒ 자신의 distance vector가 변경될 경우 이웃 노드에게 전달해줌
따라서 부분적인 정보가 전파되면서 routing table이 만들어짐

- Count-to-infinity → poison reverse(reverse path를 막아야 한다)
    
    = 이웃에게 값을 넘겨줄 때 결정적 요인을 한 노드에게는 무한대 값을 넘겨준다
    

### *Hierarchical routing

### **AS(Autonomous System)**

→ 하나의 라우팅 도메인에 대한 자치권을 가진 시스템(각각 ASNs-ASNumbers 가짐)

하나의 네트워크 단위로 생각하면 된다. 삼성, KT, Google은 각각 AS로 이들은 Inter-AS algorithm을 통해 라우팅 이루어짐.

하나의 AS 안에서는 intra-AS algorithm으로 라우팅이 이루어진다.

- Intra-AS algorithm(목적: 최단 경로): OSPF, RIP
- Inter-AS algorithm(목적: 불분명): BGP

이때 AS마다 위상이 다르다. 이들간의 관계는?

**Customers and Providers**

서비스를 사용하는 AS - customer

서비스를 제공하는 AS - provider

이때 customer은 인터넷에 엑세스 하기 위해 provider에게 돈을 지불

**“Peering” Relationship**

비슷한 체급의 AS끼리의 관계로 돈을 지불하지 않고 서로 교류, 하지만 중간 연결로의 traffic은 허용하지 않음

### BGP-4

BGP= Border Gateway Protocol

*정책 기반(policy based) 라우팅 프로토콜 → 최적화가 비용으로 이루어지지 않음

**ASPATH attribute**

AS Path에 지나가는 AS의 ASNs를 입력하여 element들로 AS hop수를 추정할 수 있다. 또한 어디로 traffic을 거쳐 올지를 결정 가능

**내가 provider위치에 있는 경로를 고르게 된다.(hop수에 상관없이 cost를 최대로 할 수 있는 경로)
